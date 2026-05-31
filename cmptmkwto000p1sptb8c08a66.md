---
title: "The one about overriding htmx's default behavior on failure"
seoDescription: "Learn how to handle complex UI validation errors in htmx while keeping your code clean and respecting Locality of Behavior (LoB)."
datePublished: 2026-05-31T10:16:54.833Z
cuid: cmptmkwto000p1sptb8c08a66
slug: the-one-about-overriding-htmx-s-default-behavior-on-failure
cover: https://cdn.hashnode.com/uploads/covers/642d433eeaad3d174f737099/3ee4fa5a-0a78-412d-ad82-d1680987eeee.png
tags: ai, css, ruby, javascript, html, ajax, hypermedia, tailwind-css, htmx, ui-components, sinatrarb, claude-code

---

The [project](/the-one-about-a-new-project-working-for-an-american-fintech) that I have been working on for the past few months is coming to an end. A few days ago, we even presented a demo of the new UI to the stakeholders, who have already started using it in production. Although the feedback has been very positive, they are used to working in a certain way with the old UI and requested some changes so that the transition to the new UI causes the least amount of friction possible.

One of those changes is related to adding new comments. Right now, the user has to click a button to add the form to the page using [htmx](https://htmx.org/). The stakeholders prefer to have the comment box always visible to streamline their workflow by requiring fewer clicks.

At the design level, we need to eliminate the button that adds the new comment form to the page and the "Cancel" button that removes said form from the page. Since it is always visible, it is no longer necessary.

At the functional level, we need to remove the endpoint that returns the HTML for the new comment form and change some details in the htmx configuration related to that logic.

> The code shown below is a simplified version, removing unnecessary noise for this post, such as authorization, logging, HTML markup, or CSS styles.

## Initial state

### Endpoints

This is a [Sinatra](https://sinatrarb.com/) application, where we have the following endpoints:

```ruby
get '/comments/new' do
  render_partial 'comments/_new_comment_form', locals: NewCommentPresenter.present
end

post '/comments' do
  comment_params = {
    content: params[:content],
    author: current_user.email
  }
  result = CreateCommentCommand.call(comment_params)
  status_code, response = extract_response(result)

  case status_code
  when HTTP_CREATED_STATUS_CODE
    render_partial 'comments/_comment',
                    locals: CommentPresenter.present(response),
                    flash: { type: :success, message: 'Comment added successfully' }
  else
    render_partial 'comments/_new_comment_form',
                    locals: NewCommentPresenter.present(comment_params, errors: response),
                    flash: { type: :error, message: 'Unable to add comment. Please try again or contact support.' }
  end
end
```

Things to highlight in this code:

*   The command returns an instance of the [Result](https://hanakai.org/learn/dry/dry-monads/result) monad, `Success` or `Failure`, provided by [dry-monads](https://hanakai.org/learn/dry/dry-monads/).
    
*   `extract_response` checks the result from the command and extracts the response using [pattern matching](https://docs.ruby-lang.org/en/4.0/syntax/pattern_matching_rdoc.html).
    
*   Depending on the `status_code`, either the newly created comment or the same form with the errors found during server validation is rendered.
    
*   Presenters only receive data and are responsible for shaping it before passing it to the partials or views.
    
*   The flash alert displays a message with feedback for the user, appearing at the top right of the page and disappearing automatically after x seconds. I would like to talk about this implementation in a later post.
    
*   The server response is implicitly [200 OK](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status/200) in both cases, so the response is always inserted into the DOM.
    

### Presentation layer

These are templates in ERB format, both views and partials:

```html
<!-- comments/index.erb -->
<h2>Comments</h2>

<%=
  link_button(
    'Add new comment',
    htmx: {
      get: '/comments/new',
      target: '#comments-container',
      swap: 'afterbegin show:#new-comment-form:top swap:50ms',
      'on::before-request': "if(document.querySelector('#new-comment-form')) { event.preventDefault() }"
    }
  )
%>

<div id="comments-container">
  <% if comments.empty? %>
    <p id="no-comments">No comments yet</p>
  <% else %>
    <% comments.each do |comment| %>
      <%= render_partial 'comments/_comment', locals: { comment: } %>
    <% end %>
  <% end %>
</div>
```

```html
<!-- comments/_new_comment_form.erb -->
<form
  id="new-comment-form"
  hx-post="/comments"
  hx-target="#comments-container"
  hx-swap="afterbegin"
  hx-select=".comment, #new-comment-form"
  hx-on::after-request="if(event.detail.successful) { document.getElementById('no-comments')?.remove(); this.remove(); }"
>
  <%=
    textarea(
      name: 'content',
      label: 'New comment',
      value: comment[:content],
      error: errors[:content],
      required: true
    )
  %>

  <%= secondary_button('Cancel', onclick: "this.closest('form').remove();") %>
  <%= primary_button('Add', type: :submit) %>
</form>
```

Things to highlight in this code:

*   [Server-rendered UI components](/the-one-with-the-ruby-dsl-for-ui-components) are used for buttons and form fields.
    
*   The new comment form is added above the list of existing comments, provided it is not already present on the page, and the view automatically scrolls to the top of the form.
    
*   The new comment or the new comment form with errors is extracted from the server response:
    
    *   In case the comment is successfully created, the new comment is added to the top of the comment list, the new comment form is removed, and the "No comments yet" message is removed.
        
    *   In case of finding any failure when creating the comment, the same form is rendered again with the errors found.
        
*   Clicking on the "Cancel" button removes the form from the page.
    

## Implementation of the required changes

### Endpoints

As I mentioned previously, the `/comments/new` endpoint is no longer necessary. Nothing changes in the other endpoint.

### Presentation layer

```html
<!-- comments/index.erb -->
<h2>Comments</h2>

<%= render_partial 'comments/_new_comment_form', locals: { comment: {} } %>

<div id="comments-container">
  <% if comments.empty? %>
    <p id="no-comments">No comments yet</p>
  <% else %>
    <% comments.each do |comment| %>
      <%= render_partial 'comments/_comment', locals: { comment: } %>
    <% end %>
  <% end %>
</div>
```

```html
<!-- comments/_new_comment_form.erb -->
<form
  id="new-comment-form"
  hx-post="/comments"
  hx-target="#comments-container"
  hx-swap="afterbegin"
  hx-select=".comment, #new-comment-form"
>
  <%=
    textarea(
      name: 'content',
      label: 'New comment',
      value: comment[:content],
      error: errors[:content],
      required: true
    )
  %>

  <%= primary_button('Add', type: :submit) %>
</form>
```

Things to highlight in this code:

*   The "Add new comment" button is replaced by the new comment form.
    
*   It is not necessary to check if the form is already present on the page using JS.
    
*   The "Cancel" button is no longer necessary.
    
*   In case the request to the server is successful, the form itself is not removed, nor is the "No comments yet" message.
    

The good news is that while removing the `hx-on::after-request` attribute, I discovered a bug in the code, which occurred only in case of finding an error on the server:

```plaintext
hx-on::after-request="if(event.detail.successful) { document.getElementById('no-comments')?.remove(); this.remove(); }"
```

> [hx-on attributes docs](https://htmx.org/attributes/hx-on/)

The key lies in the status code returned by the server, which we already mentioned is 200 OK by default, so after each request, it removed the original form and the "No comments yet" message.

The new change causes that, when submitting the form, if any validation error is found on the server, the form with the errors is rendered below the original form, which is no longer removed. The more requests that are made, the more forms will appear on the page.

Of course, the "No comments yet" message is never removed either, even when it is necessary to do so.

## A new approach

The goal is to **have a different configuration for successful and failed requests**.

We start by returning the correct status code from the server in any case that is not a success:

```ruby
post '/comments' do
  # ...
  status_code, response = extract_response(result)

  case status_code
  when HTTP_CREATED_STATUS_CODE
    render_partial 'comments/_comment',
                    locals: CommentPresenter.present(response),
                    flash: { type: :success, message: 'Comment added successfully' }
  else
    status status_code
    render_partial 'comments/_new_comment_form',
                    locals: NewCommentPresenter.present(comment_params, errors: response),
                    flash: { type: :error, message: 'Unable to add comment. Please try again or contact support.' }
  end
end
```

On the other hand, we must first configure the [htmx responses](https://htmx.org/docs/#requests), allowing the content returned by the server for [client error responses](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status#client_error_responses) to be replaced on the page:

```js
document.addEventListener("DOMContentLoaded", function() {
  htmx.config.responseHandling = [
    // ...
    { code: "4..", swap: true, error: true }
    // ...
  ]
});
```

The htmx documentation recommends using the [Response Targets extension](https://htmx.org/extensions/response-targets/), which allows you to configure the behavior of response codes declaratively via attributes.

However, that extension falls short for my use case, as it only allows specifying different target elements. I would have needed something like the following:

```html
<form
  id="new-comment-form"
  hx-post="/comments"
  hx-target="#comments-container"
  hx-swap="afterbegin"
  hx-select=".comment"
  hx-target-error="this"
  hx-swap-error="outerHTML"
  hx-select-error="#new-comment-form"
>
```

> Perhaps at some point I will have enough time to create a [custom extension](https://htmx.org/extensions/) for this use case.

Another alternative offered by htmx is to modify the [swapping behavior of the page content with events](https://htmx.org/docs/#modifying_swapping_behavior_with_events):

```html
<form
  id="new-comment-form"
  hx-post="/comments"
  hx-target="#comments-container"
  hx-swap="afterbegin"
  hx-select=".comment"
  hx-on::before-swap="
    if (event.detail.isError) {
      event.detail.target = document.getElementById('new-comment-form');
      event.detail.swapOverride = 'outerHTML';
      event.detail.selectOverride = '#new-comment-form';
    }
  "
>
```

In my opinion, it is less elegant than what I expected from the extension, but I find it to be a sufficiently good alternative.

To my surprise, defining the [htmx:beforeSwap event](https://htmx.org/events/#htmx:beforeSwap) that way does not work, because it does not execute on the requesting element, but on the target element.

It is possible to declare the event on the target element or on a common ancestor to both, but I quickly discarded that option. Imagine coming across the following code without any context:

```html
<div
  hx-on::before-swap="
    if (event.detail.isError) {
      event.detail.target = document.getElementById('new-comment-form');
      event.detail.swapOverride = 'outerHTML';
      event.detail.selectOverride = '#new-comment-form';
    }
  "
>
  <!-- the requesting element could be one of its children -->
</div>
```

Would you not wonder what on earth that is doing there?

It can make some sense if the target element or the common ancestor are defined in the same view, but if they are defined in different views, as is this case, I prefer to follow the [Locality of Behavior (LoB)](https://htmx.org/essays/locality-of-behaviour/) principle.

Here, the help of Claude Code was decisive, since it suggested using [htmx:configRequest event](https://htmx.org/events/#htmx:configRequest) instead:

```html
<form
  id="new-comment-form"
  hx-post="/comments"
  hx-target="#comments-container"
  hx-swap="afterbegin"
  hx-select=".comment"
  hx-on::config-request="
    document.addEventListener('htmx:beforeSwap', function onBeforeSwap(e) {
      document.removeEventListener('htmx:beforeSwap', onBeforeSwap);
      if (e.detail.isError) {
        e.detail.target = document.getElementById('new-comment-form');
        e.detail.swapOverride = 'outerHTML';
        e.detail.selectOverride = '#new-comment-form';
      }
    });
  "
>
```

By using [addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener), we subscribe to the event we want and define the desired target.

Knowing the tendency of AI to include more code than necessary, I questioned it on whether the use of [removeEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/removeEventListener) was truly necessary in this case.

Its arguments definitely convinced me:

*   If the listener is not removed, they would accumulate over time, since `htmx:configRequest` event is fired every time the form is submitted.
    
*   Each obsolete listener continues to execute and attempts to redirect the swap. In this case, all of them would perform the same task, so from a functional point of view, it might appear to work, but it could be a memory or event leak that grows without limits.
    

What I always do when I use AI to write code is to ask if it can do it in a simpler and/or more elegant way. On this occasion, it suggested the possibility of passing the [once argument to addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#once) to automatically remove the listener after being invoked:

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

Having reached this point, two main issues remain in the code:

*   In case of submitting the new comment form and finding validation errors on the server, the textarea renders with a red border and the corresponding error message below it. If the user subsequently fills out the form correctly and submits it, the comment is created successfully and added to the comment list, but visually the form remains as it was. Due to the applied styles and validation errors, simply performing a [reset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/reset) of the form does not work, because it basically only clears the content of the comment.
    
*   The "No comments yet" message still is not removed when the first comment is added to the page.
    

Next, I show how to solve both problems in a simple way.

In the controller, in case of successfully creating a comment, a new partial specific to this use case is rendered:

```ruby
post '/comments' do
  # ...
  status_code, response = extract_response(result)

  case status_code
  when HTTP_CREATED_STATUS_CODE
    render_partial 'comments/_comment_created',
                    locals: CommentPresenter.present(response),
                    flash: { type: :success, message: 'Comment added successfully' }
  else
    # ...
  end
end
```

In that new partial, the newly created comment is rendered, and the form is replaced with a new one by making use of the [hx-swap-oob](https://htmx.org/attributes/hx-swap-oob/) attribute, which allows you to specify that some content in a response should be swapped into the DOM somewhere other than the target:

```html
<!-- comments/_comment_created.erb -->
<%= render_partial 'comments/_comment', locals: { comment: } %>

<div hx-swap-oob="outerHTML:#new-comment-form">
  <%= render_partial 'comments/_new_comment_form', locals: { comment: {} } %>
</div>
```

And in the comment list we can apply a [CSS trick](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/:has), in this case making use of [TailwindCSS](https://tailwindcss.com/docs/hover-focus-and-other-states#has), so that it only shows the well-known "No comments yet" message when appropriate:

```html
<!-- comments/index.erb -->
<div id="comments-container" class="group/no-comments">
  <p class="group-has-[.comment]/no-comments:hidden">No comments yet</p>

  <% comments.each do |comment| %>
    <%= render_partial 'comments/_comment', locals: { comment: } %>
  <% end %>
</div>
```

Now, everything works exactly as I expected. Hooray!

## Conclusion

What started as a simple UX request – keeping a comment form always visible – turned into a great exercise in understanding the htmx event lifecycle and DOM manipulation.

It is easy to love a tool like htmx when you are cruising down the happy path. The real engineering happens when you hit a failure path and realize the library's default behavior does not match your product requirements, and have to decide how to bend the tool to your will.

Overriding htmx's default behavior on failure forced me to think about where the UI logic truly belongs. By keeping that logic inside the view layer, **Locality of Behavior** was preserved and kept the backend clean. It proves that with a solid understanding of how a framework processes requests, you do not need a heavy JavaScript framework to build complex, resilient user interfaces.

Ultimately, the stakeholders got a lower-friction UI that matches their workflow, and I got a much deeper understanding of htmx's defaults.

Thank you for reading and see you in the next one!