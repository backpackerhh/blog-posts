---
title: "The one with the Ruby DSL for UI components"
seoDescription: "Learn how to build a block DSL for server-rendered Ruby components with thread-local stacks"
datePublished: 2026-04-28T19:00:58.754Z
cuid: cmoizrr3v00je2ekic43yazea
slug: the-one-with-the-ruby-dsl-for-ui-components
cover: https://cdn.hashnode.com/uploads/covers/642d433eeaad3d174f737099/34ba6dbd-6d95-40d7-b5ed-c14af0edf898.png
tags: css, ruby, rails, html, ruby-on-rails, ui, dsl, ui-components, erb, sinatrarb

---

In a [previous post](https://davidmontesdeoca.dev/the-one-about-rendering-and-displaying-code-examples-in-erb) I talked about the internal design system I have been working on recently, consisting of server-rendered UI components, using Ruby and [ERB](https://ruby-doc.org/stdlib/libdoc/erb/rdoc/ERB.html) templates.

Most of these components are simple: you call a method and the page renders the HTML returned by the corresponding template.

Some examples of components:

```ruby
primary_button "Save"
status_badge "Finished", color: "green"
text_input name: "name", label: "Name", value: "David"
dropdown name: "country", label: "Country", options: countries
```

Of course, not all components are that simple.

## The problem

The first complex component I had to create was a table. I mentioned in the previous post that my first approach was to create a CSS-only component:

```html
<table class="table">
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

I soon realized that a component of that kind is not really useful. While it is true that defining styles is very simple this way, adding common behavior for the different applications that use it, such as making the content of the different columns sortable, is not.

## The first block DSL component: table

The goal was to create a `table` component that would allow consumers to write something like the following inside a single `<%= %>` tag:

```ruby
table do
  table_head do
    table_row do
      table_header_cell("Name", **sortable_column_params(:name, sorting_params:))
      table_header_cell("Email", **sortable_column_params(:email, sorting_params:))
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

Where `**sortable_column_params` would return a hash containing the required configuration for that column to be properly sorted:

```ruby
{ sortable: true, sort_direction: nil | "asc" | "desc", htmx: { ... } }
```

This is harder than it looks. In a plain Ruby method, each helper runs, returns a string, and the outer block has no visibility into what its children did.

The calls to each one of those methods return strings independently. There is no shared place for them to store their rendered HTML.

In situations like this, I usually look at the Rails source code to see how the framework handles it and whether there is a simple way to replicate that approach, instead of adding more dependencies to the project.

> In a Rails view, every `<%= ... %>` tag appends its result to `@output_buffer`, an `ActionView::OutputBuffer` maintained during rendering.
>
> `capture { ... }` works by swapping the buffer: it saves the current `@output_buffer`, installs a fresh one, yields the block (so any `<%= ... %>` inside writes into the new buffer), then restores the original and returns what was captured.
>
> Block-form helpers like `content_tag(:div) { ... }` use capture internally to collect their block's output before wrapping it in markup.
>
> But this only works when the caller is an ERB template rendered by `ActionView`. In plain Sinatra there is no `@output_buffer` and no `capture`, so the nested DSL above has nowhere to collect its children's output.

Next, I shared what I wanted to achieve with Claude Code in _plan mode_, and after a few iterations, I got the AI to propose an implementation that seemed simple enough.

The implementation, slightly simplified, is as follows:

```ruby
module Components
  module Table
    def table(&block)
      content = capture_table_content(&block)

      Components.load_template("table/_table.erb", binding)
    end

    def table_head(&block)
      content = capture_table_content(&block)
      result = Components.load_template("table/_head.erb", binding)

      append_to_table_buffer(result)
    end

    def table_body(&block)
      content = capture_table_content(&block)
      result = Components.load_template("table/_body.erb", binding)

      append_to_table_buffer(result)
    end

    def table_row(&block)
      content = capture_table_content(&block)
      result = Components.load_template("table/_row.erb", binding)

      append_to_table_buffer(result)
    end

    def table_header_cell(content, **sortable_params)
      result = Components.load_template("table/_header_cell.erb", binding)

      append_to_table_buffer(result)
    end

    def table_cell(content)
      result = Components.load_template("table/_cell.erb", binding)

      append_to_table_buffer(result)
    end

    # other helpers omitted

    private

    def table_buffer_stack
      Thread.current[:table_buffer_stack] ||= []
    end

    def capture_table_content(&block)
      table_buffer_stack.push([])
      block.call
      table_buffer_stack.pop.join
    end

    def append_to_table_buffer(html)
      table_buffer_stack.last << html if table_buffer_stack.any?

      html
    end
  end
end
```

Two ideas are doing the work here:

- **A stack of arrays** instead of a single buffer, so nested blocks do not stomp on each other:

  ```ruby
  table do              # buffer_stack = [[]]
    table_body do       # buffer_stack = [[], []]
      table_row do      # buffer_stack = [[], [], []]
        table_cell "a"  # buffer_stack = [[], [], ["<td>a</td>"]]
        table_cell "b"  # buffer_stack = [[], [], ["<td>a</td>", "<td>b</td>"]]
      end               # buffer_stack = [[], ["<tr><td>a</td><td>b</td></tr>"]]
    end                 # buffer_stack = [["<tbody>…</tbody>"]]
  end                   # returns "<table><tbody>…</tbody></table>"
  ```

  `table_body` pushes a fresh array; its inner `table_row` pushes another on top; when each `pop`s, the content of the inner stack merges back into the outer one via `append_to_table_buffer`. Only the outermost `table` returns anything, the joined string after the last pop. Every other call's return value is thrown away on purpose: if `capture_table_content` trusted the block's return value, Ruby's last-expression rule would hand back only the last `table_cell`'s HTML and every earlier cell would vanish.

  Nesting a table *inside* another table works for the same reason, an inner `table do` just pushes one more array onto the stack, fills it, pops it, and deposits the whole inner `<table>…</table>` string onto its parent's array like any other child's output.

- **Thread-local storage**, because Sinatra on a threaded server, like [Puma](https://github.com/puma/puma), handles requests in parallel:

  ```ruby
  Thread.current[:table_buffer_stack]
  ```

  If `table_buffer_stack` were a class-level `@@buffer_stack` instead, two requests entering `table do` at the same moment would push onto the same array; each one's `table_cell` could append into the other's array, mixing rows across responses. `Thread.current` gives each thread its own stack, so the two requests can not see each other's state.

All that works perfectly for the table component, but it does not for the next component I needed to create.

## The second block DSL component: modal

The goal was to create a `modal` component that would allow consumers to write something like the following inside a single `<%= %>` tag:

```ruby
modal do
  modal_header do
    "Confirm status change"
  end
  modal_body do
    <<~CONTENT
      <p>Do you confirm the change of status?</p>
      <p><span>OLD_STATUS</span> -> <span>NEW_STATUS</span></p>
    CONTENT
  end
  modal_footer do
    modal_cancel
    modal_action "Confirm", htmx: { ... }
  end
end
```

The implementation is basically the same as for the `table` component:

```ruby
module Components
  module Modal
    def modal(&block)
      content = capture_modal_content(&block)

      Components.load_template("modal/_modal.erb", binding)
    end

    # other helpers omitted

    private

    def modal_buffer_stack
      Thread.current[:modal_buffer_stack] ||= []
    end

    def capture_modal_content(&block)
      modal_buffer_stack.push([])
      result = block.call
      buffer = modal_buffer_stack.pop

      buffer.empty? ? result.to_s : buffer.join
    end
  end
end
```

There are only two differences:

* The thread-local key used to store the component's content in `Thread.current` is different, `:modal_buffer_stack`.

* There is an important change in the method that captures the component's content:

  ```ruby
  buffer.empty? ? result.to_s : buffer.join
  ```

  It falls back to the block's return value if the block did not call any DSL child.

  For instance:

  ```ruby
  modal_header { "Confirm status change" }
  ```

  The block returns a bare string, so nothing is pushed to the buffer. Without the fallback, those blocks would render empty.

  Table got away with `pop.join` because every `table` block is expected to contain child DSL calls (`table_row`, `table_cell`, etc.).

That code works perfectly for both components, but there is too much duplicated plumbing behind both components.

## Extracting the common logic

The next step was to extract the common logic for all components that implement a block DSL into a module so it can be reused:

```ruby
module ContentBuffer
  def self.included(base)
    base.extend(ClassMethods)
  end

  module ClassMethods
    def content_buffer_key(key)
      define_method(:_content_buffer_key) { key }

      private :_content_buffer_key
    end
  end

  private

  def buffer_stack
    Thread.current[_content_buffer_key] ||= []
  end

  def capture_content(&block)
    buffer_stack.push([])
    result = block.call
    buffer = buffer_stack.pop

    buffer.empty? ? result.to_s : buffer.join
  end

  def append_to_buffer(html)
    buffer_stack.last << html if buffer_stack.any?

    html
  end
end
```

A class-level setter handles the thread-local key name in the clients of the new module:

```ruby
module Components
  module Table
    include ContentBuffer

    content_buffer_key :table_buffer_stack

    def table(&block)
      content = capture_content(&block)

      Components.load_template("table/_table.erb", binding)
    end

    def table_head(&block)
      content = capture_content(&block)
      result = Components.load_template("table/_head.erb", binding)

      append_to_buffer(result)
    end

    # other helpers omitted
  end
end
```

```ruby
module Components
  module Modal
    include ContentBuffer

    content_buffer_key :modal_buffer_stack

    def modal(&block)
      content = capture_content(&block)

      Components.load_template("modal/_modal.erb", binding)
    end

    def modal_header(&block)
      content = capture_content(&block)
      result = Components.load_template("modal/_header.erb", binding)

      append_to_buffer(result)
    end

    # other helpers omitted
  end
end
```

Besides, extracting that logic into a module makes testing easier.

To do this, we first create a couple of test components:

```ruby
module TestComponents
  module Container
    include ContentBuffer

    content_buffer_key :container_buffer_stack

    def container(&block)
      capture_content(&block)
    end

    def item(content)
      result = "<item>#{content}</item>"

      append_to_buffer(result)
    end

    def group(&block)
      content = capture_content(&block)
      result = "<group>#{content}</group>"

      append_to_buffer(result)
    end
  end

  module Widget
    include ContentBuffer

    content_buffer_key :widget_buffer_stack

    def widget(&block)
      capture_content(&block)
    end

    def widget_part(content)
      result = "<part>#{content}</part>"

      append_to_buffer(result)
    end
  end
end
```

Then we add the following test examples:

```ruby
RSpec.describe ContentBuffer do
  let(:component) do
    Class.new do
      include TestComponents::Container
    end.new
  end

  after do
    Thread.current[:container_buffer_stack] = nil
  end

  describe "block return value fallback" do
    it "returns the block return value when no items are appended" do
      result = component.container { "plain text" }

      expect(result).to eq("plain text")
    end

    it "converts non-string return values to string" do
      result = component.container { 42 }

      expect(result).to eq("42")
    end
  end

  describe "buffered content" do
    it "joins appended items" do
      result = component.container do
        component.item("one")
        component.item("two")
        component.item("three")
      end

      expect(result).to eq("<item>one</item><item>two</item><item>three</item>")
    end

    it "ignores the block return value when items are appended" do
      result = component.container do
        component.item("buffered")
        "ignored"
      end

      expect(result).to eq("<item>buffered</item>")
    end
  end

  describe "nested captures" do
    it "inner group does not leak into outer container" do
      result = component.container do
        component.item("before")

        component.group do
          component.item("nested")
        end

        component.item("after")
      end

      expect(result).to eq("<item>before</item><group><item>nested</item></group><item>after</item>")
    end
  end

  describe "isolation between different buffer keys" do
    let(:other_component) do
      Class.new do
        include TestComponents::Widget
      end.new
    end

    after do
      Thread.current[:widget_buffer_stack] = nil
    end

    it "two components with different keys do not interfere" do
      widget_result = nil

      container_result = component.container do
        component.item("a")

        widget_result = other_component.widget do
          other_component.widget_part("b")
        end

        component.item("c")
      end

      expect(container_result).to eq("<item>a</item><item>c</item>")
      expect(widget_result).to eq("<part>b</part>")
    end
  end
end
```

## The third block DSL component: alert

The goal was to create an `alert` component that would allow consumers to write something like the following inside a single `<%= %>` tag:

```ruby
alert(variant: :success) do
  alert_icon
  alert_title { "Payment processed successfully" }
  alert_description { "The transaction has been completed." }
  alert_actions do
    alert_action("View details", variant: :primary, htmx: { ... })
    alert_action("Dismiss", variant: :secondary, htmx: { ... })
  end
end
```

The `variant` should set the background color and the border, as well as the icon to display and its color, if `alert_icon` is included in the alert.

This poses a problem because `alert_icon`, if it receives no arguments, has to render the default icon according to the variant defined in the parent. However, the buffer does not allow a child to access data from the parent.

Furthermore, the alert's icon and actions are not rendered in the same block as the title and description. The template does not receive a single flat content string to print; instead, it needs the icon and actions separately, as independent variables, to place them in their respective spots. The buffer at this point only returns a plain string with everything concatenated and is incapable of separating that out.

The code that solves these problems is the following:

```ruby
def context_stack
  Thread.current[:"#{_content_buffer_key}_context"] ||= []
end

def set_context(metadata)
  context_stack.push(metadata)
end

def reset_context
  context_stack.pop
end

def current_context
  context_stack.last || {}
end
```

Therefore, the `ContentBuffer` module now has two main parts:

- **Buffer**: a linear, append-only stream of HTML fragments from children.
- **Context**: a shared hash the parent seeds, that children can read from and write into.

> The context key is derived from the same `_content_buffer_key` the buffer uses, with a `_context` suffix appended. This way, each component that includes `ContentBuffer` ends up with its own isolated buffer stack and context stack.

The context uses a stack, rather than a single hash, for the same reason the buffer does: a component nested inside another cannot overwrite the parent's context.

The implementation of the `alert` component, slightly simplified, is as follows:

```ruby
module Components
  module Alert
    include ContentBuffer

    content_buffer_key :alert_buffer_stack

    VARIANTS = {
      success: {
        ...
      },
      error: {
        ...
      },
    }.freeze

    def alert(variant:, &block)
      set_context(variant:, icon: nil, actions: nil)

      content = capture_content(&block)
      alert_icon = current_context[:icon]
      alert_actions = current_context[:actions]

      reset_context

      Components.load_template("alert/_alert.erb", binding)
    end

    def alert_icon(icon_name = nil)
      variant = current_context[:variant]

      current_context[:icon] = icon(icon_name || VARIANTS.dig(variant, :icon_name))

      ""
    end

    def alert_title(&block)
      content = capture_content(&block)
      result = Components.load_template("alert/_title.erb", binding)

      append_to_buffer(result)
    end

    def alert_description(&block)
      content = capture_content(&block)
      result = Components.load_template("alert/_description.erb", binding)

      append_to_buffer(result)
    end

    def alert_actions(&block)
      content = capture_content(&block)

      current_context[:actions] = Components.load_template("alert/_actions.erb", binding)

      ""
    end

    def alert_action(text, **options)
      result = button(text, **options)

      append_to_buffer(result)
    end
  end
end
```

Two points to highlight here:

- `alert_icon` and `alert_actions` return an empty string. Instead of writing to the buffer, they store their rendered HTML in a named slot in the context (`:icon` and `:actions` respectively). The parent `alert` method reads those slots into its own local variables after running the block, and passes them to the template as independent variables.
- `alert_title` and `alert_description` write to the buffer exactly like table and modal children do, so they end up in the concatenated content.

A single alert uses both mechanisms: title and description go into the concatenated content, icon and actions into named slots.

Next, we create a couple more test components:

```ruby
module TestComponents
  # other code omitted

  module Panel
    include ContentBuffer

    content_buffer_key :panel_buffer_stack

    def panel(variant:, &block)
      set_context(variant:, icon: nil)

      content = capture_content(&block)
      icon = current_context[:icon]

      reset_context

      "<panel variant='#{variant}' icon='#{icon || "none"}'>#{content}</panel>"
    end

    def panel_icon
      variant = current_context[:variant]

      current_context[:icon] = "icon-for-#{variant}"

      ""
    end

    def panel_body(content)
      append_to_buffer("<body>#{content}</body>")
    end

    def read_current_context
      current_context
    end
  end

  module Banner
    include ContentBuffer

    content_buffer_key :banner_buffer_stack

    def banner(variant:, &block)
      set_context(variant:)

      content = capture_content(&block)

      reset_context

      "<banner variant='#{variant}'>#{content}</banner>"
    end

    def read_current_context
      current_context
    end
  end
end
```

And we add the following test examples:

```ruby
RSpec.describe ContentBuffer do
  # other code omitted

  describe "context stack" do
    let(:panel_component) do
      Class.new do
        include TestComponents::Panel
      end.new
    end
    let(:banner_component) do
      Class.new do
        include TestComponents::Banner
      end.new
    end

    after do
      Thread.current[:panel_buffer_stack] = nil
      Thread.current[:panel_buffer_stack_context] = nil
      Thread.current[:banner_buffer_stack] = nil
      Thread.current[:banner_buffer_stack_context] = nil
    end

    it "returns empty hash when no context is set" do
      expect(panel_component.read_current_context).to eq({})
    end

    it "sets and clears context" do
      inside_context = nil

      panel_component.panel(variant: :success) do
        inside_context = panel_component.read_current_context
      end

      expect(inside_context).to eq(variant: :success, icon: nil)
      expect(panel_component.read_current_context).to eq({})
    end

    it "nests contexts" do
      nested_context = nil
      outer_context_after = nil

      panel_component.panel(variant: :info) do
        panel_component.panel(variant: :error) do
          nested_context = panel_component.read_current_context
        end

        outer_context_after = panel_component.read_current_context
      end

      expect(nested_context).to eq(variant: :error, icon: nil)
      expect(outer_context_after).to eq(variant: :info, icon: nil)
    end

    it "isolates context between different buffer keys" do
      panel_context = nil
      banner_context = nil

      panel_component.panel(variant: :success) do
        banner_component.banner(variant: :error) do
          panel_context  = panel_component.read_current_context
          banner_context = banner_component.read_current_context
        end
      end

      expect(panel_context).to eq(variant: :success, icon: nil)
      expect(banner_context).to eq(variant: :error)
    end
  end
end
```

## The full ContentBuffer module

Putting it all together, the final `ContentBuffer` module looks like this:

```ruby
module ContentBuffer
  def self.included(base)
    base.extend(ClassMethods)
  end

  module ClassMethods
    def content_buffer_key(key)
      define_method(:_content_buffer_key) { key }

      private :_content_buffer_key
    end
  end

  private

  def buffer_stack
    Thread.current[_content_buffer_key] ||= []
  end

  def capture_content(&block)
    buffer_stack.push([])
    result = block.call
    buffer = buffer_stack.pop

    buffer.empty? ? result.to_s : buffer.join
  end

  def append_to_buffer(html)
    buffer_stack.last << html if buffer_stack.any?

    html
  end

  def context_stack
    Thread.current[:"#{_content_buffer_key}_context"] ||= []
  end

  def set_context(metadata)
    context_stack.push(metadata)
  end

  def reset_context
    context_stack.pop
  end

  def current_context
    context_stack.last || {}
  end
end
```

## Possible improvements

The module is simple and covers every block DSL component I have needed so far, but there are still a few rough edges worth calling out before wrapping up, although it is not meant to be a thorough list:

1. **Exception safety**: `capture_content` pushes and pops the buffer stack by hand, and `alert` does the same with `set_context` and `reset_context`. If the block raises in between, the stack keeps the orphaned frame. A `begin/ensure` around each pair fixes both.

2. **Explicit buffer-vs-context contract**: Writing to the buffer means returning a string; writing to a slot means returning `""` and assigning into the context by hand. A helper like `with_slot(slot_name) { ... }` would make the slot write the block's explicit purpose instead of an empty-string side effect.

3. **Typed context slots**: The slots `:icon`, `:actions`, and `:variant` are untyped hash keys, so a typo silently returns `nil`. A `context_slots` class macro, alongside `content_buffer_key`, would declare each component's slots and catch typos.

4. **Rename the module**: `ContentBuffer` fit when there was only a buffer. With the context stack in place, it should have a name that fits better.

5. **Extend module API**: add a method to reset the values stored in the tests, instead of assigning `nil` in an `after` block.

## Conclusion

Building your own component system from scratch forces you to face problems that frameworks like Rails solve
for you. Once you solve them yourself, even in a small way, they stop feeling like magic.

The `ContentBuffer` module is not trying to be a general-purpose abstraction. It only has to work for the
components that use it today, and that is why it stays small.

In exchange, what you get is Rails-like ergonomics on top of ERB in Sinatra, with no extra dependencies. There is
still room for improvement, but the core is simple and easy to test.

If you have an alternative approach to this problem, feel free to share it with me.

Thank you for reading, and see you in the next one!