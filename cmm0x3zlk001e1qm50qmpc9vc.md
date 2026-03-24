---
title: "The one about rendering and displaying code examples in ERB"
seoDescription: "Learn how to build a DRY component showcase in Sinatra. Define ERB examples once to both render the UI component and display its source code."
datePublished: 2026-02-24T18:07:14.940Z
cuid: cmm0x3zlk001e1qm50qmpc9vc
slug: the-one-about-rendering-and-displaying-code-examples-in-erb
cover: https://cdn.hashnode.com/uploads/covers/642d433eeaad3d174f737099/4a71704b-945a-4785-a58e-65acf68f2309.png
tags: ruby, dry, design-systems, erb, sinatrarb

---

I have been building a component showcase page for an internal design system, using [Sinatra](https://sinatrarb.com/) and [ERB](https://ruby-doc.org/stdlib/libdoc/erb/rdoc/ERB.html) templates. The page renders each UI component visually and, right below it, offers a toggle to reveal the source code that produced it.

## The problem: code duplication

The naive approach looks like this:

```erb
<div class="example">
  <%= primary_button "Button" %>
</div>

<%= erb :_toggle_code, locals: { code: 'primary_button "Button"' } %>
```

The first block calls the Ruby helper to render the actual button. The second block passes the same call as a string to a partial that displays it:

```erb
<code><%= code %></code>
```

This works, but the code is duplicated. Update one, forget the other, and they drift apart. With dozens of examples, that drift becomes inevitable.

## Single source of truth

The idea is straightforward: store the code as a string, then `eval` it to render the component, and display the source.

I wrapped the logic in a helper module:

```ruby
def render_code_example(code)
  eval(code.strip, binding)
end
```

> Disclaimer: this is an internal tool, never use this pattern with untrusted or user-generated input.

That worked for simple, single-line examples:

```erb
<div class="example">
  <% example = 'primary_button "Button"' %>

  <%= render_code_example(example) %>

  <%= erb :_toggle_code, locals: { code: example } %>
</div>
```

However, I found that examples with multiple lines, like a fieldset containing several radio buttons, would only render the last one. `eval` returns the value of the last expression, so intermediate lines were lost:

```erb
<div class="example">
  <%
    example = <<-CODE
      radio_input(name: "contact", value: "email", id: "contact_email", label: "Email")
      radio_input(name: "contact", value: "phone", id: "contact_phone", label: "Phone")
    CODE
  %>

  <fieldset>
    <legend>Select your preferred contact method:</legend>
    <%= render_code_example(example) %>
  </fieldset>

  <%= erb :_toggle_code, locals: { code: example } %>
</div>
```

The fix was to split the string into lines and eval each one independently:

```ruby
def render_code_example(code)
  code.strip.split("\n").map { |line| eval(line.strip, binding) }.join("\n")
end
```

The method splits the string into lines, evaluates each one in the current [binding](https://ruby-doc.org/core/Binding.html) (which has access to all the component helper methods), and joins the results. Each line is an independent Ruby expression that returns HTML.

The example is now defined once: `render_code_example` executes it, and the `_toggle_code` partial displays it.

That solved the rendering side, but the output code was not quite right. The `<code>` element collapses whitespace by default, so both radio buttons are shown in a single line:

```erb
radio_input(name: "contact", value: "email", id: "contact_email", label: "Email") radio_input(name: "contact", value: "phone", id: "contact_phone", label: "Phone")
```

Wrapping the output in a `<pre>` tag preserves the line breaks:

```erb
<pre><code><%= code %></code></pre>
```

That fixed the formatting, but introduced a new issue. The heredoc (`<<-CODE`) preserves the indentation from the ERB template, so the output code was rendered with leading spaces:

```erb
            radio_input(name: "contact", value: "email", id: "contact_email", label: "Email")
            radio_input(name: "contact", value: "phone", id: "contact_phone", label: "Phone")
```

Switching to a [squiggly heredoc](https://docs.ruby-lang.org/en/master/syntax/literals_rdoc.html#here-document-literals) (`<<~CODE`) stripped the common leading indentation automatically.

Finally, since the code string may contain HTML characters, the partial escapes it before rendering:

```erb
<pre><code><%= escape_code_example(code) %></code></pre>
```

That new helper uses `Rack::Utils.escape_html` to handle the escaping.

This eliminated the duplication problem for all examples where each line is an independent Ruby expression.

## The new challenge: HTML components

Then a new table component was needed, and instead of building a Ruby helper the first approach was a CSS-only component: plain HTML with custom styles.

```html
<table class="fw-table">
  <thead>
    <tr>
      <th>Name</th>
      <th>Email</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Alice</td>
      <td>alice@example.com</td>
    </tr>
  </tbody>
</table>
```

You cannot `eval` HTML. It is not Ruby. A different approach was needed.

When ERB processes a template, it appends the rendered content to a string buffer. In Sinatra, that buffer is the instance variable [@\_out\_buf](https://github.com/sinatra/sinatra/blob/v4.1.1/lib/sinatra/base.rb#L866). The idea was to record the buffer's length before a block executes, let the block run, and then slice out everything that was appended during it:

```ruby
def capture_html(&block)
  buffer = @_out_buf
  pos = buffer.length
  yield
  dedent(buffer.slice!(pos..-1))
end
```

The `slice!` call is important: it removes the captured content from the buffer so it is not rendered twice.

There was one more problem. The captured HTML carries the indentation of the ERB template it lives in. Since the code display uses a `<pre>` tag, that indentation shows up as unwanted leading spaces.

A `dedent` helper strips the common leading whitespace, normalizing the output regardless of how deeply nested the ERB block was:

```ruby
def dedent(text)
  margin = text.scan(/^[ \t]*(?=\S)/).map(&:size).min || 0

  text.gsub(/^[ \t]{#{margin}}/, '').strip
end
```

With those two helpers in place, the ERB usage followed the same single-definition pattern:

```erb
<% example = capture_html do %>
  <table class="fw-table">
    <thead>
      <tr>
        <th>Name</th>
        <th>Email</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Alice</td>
        <td>alice@example.com</td>
      </tr>
    </tbody>
  </table>
<% end %>

<%= example %>

<%= erb :_toggle_code, locals: { code: example } %>
```

Write the HTML once. `capture_html` grabs it as a string. Output it for rendering, pass it for display.

## Unification: block detection

The CSS-only table did not last long. It was eventually replaced with a Ruby block DSL, where the table is built through nested method calls:

```ruby
table do
  table_head do
    table_row do
      table_header_cell("Name")
      table_header_cell("Email")
    end
  end
  table_body do
    table_row do
      table_cell("Alice")
      table_cell("alice@example.com")
    end
  end
end
```

Now `render_code_example` had to handle this too. But unlike the radio buttons from before, these lines are not independent expressions. They form a single `do...end` block, and evaluating `table do` in isolation is a syntax error.

The fix was to detect whether the code is a block expression and, if so, evaluate it as a whole instead of line by line:

```ruby
def render_code_example(code)
  stripped = code.strip

  if block_expression?(stripped)
    eval(stripped, binding)
  else
    stripped.split("\n").map { |line| eval(line.strip, binding) }.join("\n")
  end
end

private

def block_expression?(code)
  code.match?(/\bend\z/)
end
```

The check is simple: if the code ends with the `end` keyword, treat it as a block. `\b` ensures it matches the whole word, and `\z` anchors to the end of the string.

In the ERB template, multi-line code uses a [heredoc](https://ruby-doc.org/core/doc/syntax/literals_rdoc.html#label-Here+Documents+-28Heredocs-29) to keep things readable:

```erb
<%
  example = <<~CODE
    table do
      table_head do
        table_row do
          table_header_cell("Name")
          table_header_cell("Email")
        end
      end
      table_body do
        table_row do
          table_cell("Alice")
          table_cell("alice@example.com")
        end
      end
    end
  CODE
%>

<%= render_code_example(example) %>

<%= erb :_toggle_code, locals: { code: example } %>
```

With this change, `capture_html` and `dedent` were no longer needed. Every component in the showcase now uses Ruby helpers, so the buffer-interception approach was removed entirely.

## Conclusion

The core idea never changed: define each example once, then use it for both rendering and display. What evolved was the mechanism: from `eval` on single lines, to line-by-line splitting, to buffer interception for raw HTML, and finally to block detection when the Ruby DSL arrived. Each iteration solved a real problem introduced by the previous one.

The final code is straightforward, but it earned that simplicity one edge case at a time:

```ruby
module Helpers
  module CodeExample
    def escape_code_example(*strings)
      Rack::Utils.escape_html(strings.join("\n\n"))
    end

    def render_code_example(code)
      stripped = code.strip

      if block_expression?(stripped)
        eval(stripped, binding)
      else
        stripped.split("\n").map { |line| eval(line.strip, binding) }.join("\n")
      end
    end

    private

    def block_expression?(code)
      code.match?(/\bend\z/)
    end
  end
end
```

If you have an interesting alternative approach to this problem, feel free to share it with me.

Thank you for reading and see you in the next one!