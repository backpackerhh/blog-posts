---
title: "The one about choosing where htmx logic belongs"
seoDescription: "Compare view-level events and server response headers to handle htmx failures. Explore the trade-offs and decide where your logic truly belongs."
datePublished: 2026-06-22T21:09:14.210Z
cuid: cmqppkjkw00000bj7cakd4owq
slug: the-one-about-choosing-where-htmx-logic-belongs
cover: https://cdn.hashnode.com/uploads/covers/642d433eeaad3d174f737099/5bc78409-7883-4e5b-9ec4-afc3251424e8.png
tags: http, javascript, html, security, hypermedia, htmx

---

In the [previous post](/the-one-about-overriding-htmx-s-default-behavior-on-failure) I talked about how to override htmx's default behavior on failure. The proposed solution relied on [events](https://htmx.org/events/) to handle any server-side error directly from the view.

Although I recommend reading that post before moving on, here is the most relevant code from the [proposed approach](/the-one-about-overriding-htmx-s-default-behavior-on-failure#a-new-approach), which handles errors through htmx attributes defined directly on the form:

```html
<form
  id="new-comment-form"
  hx-post="/comments"
  hx-target="#comments-container"
  hx-swap="afterbegin"
  hx-select=".comment"
  hx-on::config-request="
    document.addEventListener('htmx:beforeSwap', function(e) {
      if (e.detail.isError) {
        e.detail.target = document.getElementById('new-comment-form');
        e.detail.swapOverride = 'outerHTML';
        e.detail.selectOverride = '#new-comment-form';
      }
    }, { once: true });
  "
>
```

From now on, I will assume you know how that snippet works.

I have also been thinking about an alternative way to achieve the same behavior, using a different approach. Not necessarily better.

I will first present the alternative version of that code and then lay out the pros and cons of both options.

> The code shown below is a simplified version, removing unnecessary noise for this post, such as authorization, logging, HTML markup, or CSS styles.

## A new alternative

This approach keeps the form's attributes to a minimum and lets the server tell htmx what to do through [response headers](https://htmx.org/reference/#response_headers) on failure.

### Endpoints

```ruby
post '/comments' do
  comment_params = {
    content: params[:content],
    author: user_email
  }
  result = CreateCommentCommand.call(comment_params)
  status_code, response = extract_response(result)

  case status_code
  when HTTP_CREATED_STATUS_CODE
    # ...
  else
    htmx_retarget("#new-comment-form")
    htmx_reselect("#new-comment-form")
    htmx_reswap("outerHTML")

    # ...
  end
end
```

```ruby
module Request
  HTMX_RETARGET_HEADER_NAME = 'HX-Retarget'
  HTMX_RESELECT_HEADER_NAME = 'HX-Reselect'
  HTMX_RESWAP_HEADER_NAME = 'HX-Reswap'

  def htmx_retarget(target)
    response.set_header(HTMX_RETARGET_HEADER_NAME, target)
  end

  def htmx_reselect(target)
    response.set_header(HTMX_RESELECT_HEADER_NAME, target)
  end

  def htmx_reswap(swap)
    response.set_header(HTMX_RESWAP_HEADER_NAME, swap)
  end
end
```

Things to highlight in this code:

* The endpoint's behavior stays exactly the same, except for the call to the custom htmx helpers when any error is encountered while creating a comment.
* Those helpers are defined in a module that is included in the application's base controller.
* Those helpers set the corresponding _response header_, overriding the value defined in the matching htmx attribute on the requesting element — the form, in our case.

### Presentation layer

```html
<!-- comments/_new_comment_form.erb -->
<form
  id="new-comment-form"
  hx-post="<%= comments_url %>"
  hx-target="#comments-container"
  hx-swap="afterbegin"
  hx-select=".comment"
>
  <!-- ... -->
</form>
```

Things to highlight in this code:

* Only the attributes needed for the success case are defined in the form.

## Comparing both approaches

Same DOM result, same htmx contract, two very different places to put the logic. Let's compare them honestly.

### Approach A - View layer

#### Pros

* **Co-location of behavior**: Selector, swap strategy, and retarget element all live next to the form they affect. Somebody opening the view for the first time is able to see the full lifecycle without jumping into the controller. This is the [Locality of Behavior (LoB)](https://htmx.org/essays/locality-of-behaviour/) principle that htmx itself advocates.
* **Resilience to renames**: The form ID lives in a single file. Rename it and the form keeps working — there is no controller silently pointing at an old selector.
* **Controller stays HTTP-pure**: The server returns `4xx` and an HTML fragment. It knows nothing about DOM IDs or htmx swap mechanics.
* **Reusable across endpoints**: If multiple endpoints could fail and target the same form, the form itself owns the retarget logic. None of those endpoints have to remember to set the same headers to make it work.

#### Cons

* **Inline JavaScript inside a string**: If you want to stick to the LoB principle, you have to write the JS code inside an HTML attribute that is just a string. Your IDE treats it as HTML — no syntax highlighting, no autocomplete, no jump-to-definition — and debugging is harder.
* **Duplication**: Every form needing this behavior copies essentially the same `hx-on::config-request` block, with only the target ID changing. The more forms you have, the more duplication. A Ruby helper could render that block but then it would just hide the JS code inside a string in a different file, breaking the LoB principle.
* **Status-code blindness**: The view cannot easily differentiate `422` (validation error) from `409` (conflict) from `500` (server error) without growing the inline script.
* **CSP friction**: If the app does not enforce a strict [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP), this is a non-issue. But if it does — or might in the future — `hx-on::*` attributes force you into either `'unsafe-inline'` (which weakens XSS protection across the entire site) or per-attribute `'unsafe-hashes'` (which you have to recompute every time the attribute changes).

### Approach B - Controller layer

#### Pros

* **Documented contract**: `HX-Retarget`, `HX-Reswap`, and `HX-Reselect` are first-class htmx response headers — stable across versions, no clever workaround, no mutation of internal event details.
* **Status-code routing**: The controller already knows whether it returned `422` (validation error), `409` (conflict), or `500` (server error), and can trivially return different swap behaviors for each scenario.
* **No inline JS**: Pure HTTP response. Passes strict CSP without exceptions.
* **Debuggability**: Easier to debug with a simple `curl -i` or a glance at the network tab, revealing exactly what the server instructed the client to do.

#### Cons

* **Leaks in the controller**: DOM IDs (`"#new-comment-form"`) and swap strategy (`"outerHTML"`) are presentation concerns leaking into the HTTP layer.
* **Silent breakage on rename**: Rename the form ID and the controller would break silently.
* **Drift risk**: Every endpoint that could fail and target the same form must set the same headers.
* **No co-location of behavior**: Somebody opening the view for the first time is not able to easily see how errors are handled.

## The tie-breakers

Both approaches work. Both use documented htmx machinery. Here are the factors that I think should help you decide.

### Where does this knowledge belong?

The knowledge being expressed is: 

> When this form fails to submit, replace itself with the server's re-rendered version showing errors.

- The server's job is to validate the input, return `422`, and render the form.
- The form's job is to replace itself with the server's response if the submission fails.
- The thing being expressed is therefore form behavior, not server behavior.

By that lens, the view layer wins. The controller should not care that the response is going into a form, a modal, or a toast. It returns a `422` and how the UI displays that is the UI's problem.

### Content Security Policy

If your app already enforces a strict CSP, the question is decided for you — `hx-on::*` attributes are off the table without weakening XSS protection across the whole site. If your app might enforce one in the future, you are choosing between paying the migration cost later or staying on the controller side now.

By that lens, the controller approach wins — but only if CSP is on your roadmap.

### Codebase consistency

In an existing codebase with established patterns, a slightly misaligned but consistently applied convention is more valuable than two competing patterns.

For instance, if your codebase already uses `htmx_retarget` or `htmx_reswap` somewhere, introducing `hx-on::config-request` for one new form forces every future reader to ask "why is this one different?", with no good answer. The same applies in reverse — if every existing form uses `hx-on::*`, dropping headers into the controller for this one form has the same problem.

By that lens, the existing convention should usually win. The cost of having two patterns to maintain is almost always higher than the cost of putting one of them in a slightly suboptimal layer.

## Conclusion

Both approaches use documented htmx machinery, both produce the same DOM result, and both are cheap to refactor away from. The choice is not really about which one is correct — it is about which one fits the codebase you already have.

In my case, I ultimately chose the controller layer because we use `htmx_*` helpers in other parts of the application. The "codebase consistency" tie-breaker outweighed the "knowledge belongs in the view" argument — and I think that is the right call more often than not, even when it feels architecturally less pure.

Pick the boundary that fits your team and codebase, write it down, and move on.

Thank you for reading and see you in the next one!