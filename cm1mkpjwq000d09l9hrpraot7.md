---
title: "The one about conditionals in Ruby"
seoTitle: "Understanding conditionals in Ruby"
seoDescription: "An in-depth look at conditional statements in Ruby and arguments against using the unless statement for readability"
datePublished: Sat Sep 28 2024 19:57:12 GMT+0000 (Coordinated Universal Time)
cuid: cm1mkpjwq000d09l9hrpraot7
slug: the-one-about-conditionals-in-ruby
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1727548097607/573dff93-6e49-4330-9d17-abcb020cdca9.png
tags: ruby, ruby-on-rails, linter, rubocop, conditional-statement, conditionals

---

Conditionals control the flow of execution of your program based on conditions that you define. In Ruby we have **conditional statements** such as `if`, `else`, `elsif`, `unless`, `case`, or the ternary operator.

Here I would like to focus on what I consider to be in most cases a questionable use of `unless` and what I think could be a better approach. YMMV.

But first, I will start giving a bit of context about that conditional statement.

As far as I know, [Perl](https://www.perl.org/) was the first one that introduced `unless` to provide a more readable alternative to using negated `if`.

Although the keyword `unless` is not available in most programming languages, Ruby already included it in its first version released in 1995. Other languages highly inspired by Ruby, such as [Crystal](http://crystal-lang.org/) or [Elixir](https://elixir-lang.org/), include that **control structure** as well.

Its function is the opposite of the `if` statement, meaning that will **execute a block of code only if a condition is false**. You could say it is *syntactic sugar* for a negated `if`.

> Remember that the only *falsey* values in Ruby, those that evaluate to false, are `false` and `nil`.

I have read a lot about how `unless` improves readability in cases where you are checking for negative conditions, making the code flow more naturally and even closer to plain English.

And I wonder, is that so?

Most of the people I have worked with come to Ruby from other languages, such as .NET, PHP, Python or Java. I have asked many of them what is their opinion about the use of `unless` in Ruby and not a single one of them has shared with me a positive experience. Ever.

They usually have expressed confusion towards any code where that statement is used. Of course, you could think it is a small sample to draw many conclusions. However, if you search the web you will find [other](https://stackoverflow.com/questions/13245001/cant-understand-unless-keyword-in-ruby) [people](https://www.reddit.com/r/ruby/comments/ob8f6k/is_it_just_me_or_is_unless_needlessly_confusing/) as confused as my teammates.

I tend to agree, but I must say it was not always like that. In my first contact with Ruby, I kind of liked using `unless` whenever I considered it was necessary without much hesitation.

However, nowadays I avoid its use as much as possible. In my opinion, it **adds cognitive load** in most cases. Every single time I see an `unless` in the code I have to stop and **translate it in my head to "if not"**. Even the easiest condition.

And I have been working with Ruby for almost 15 years now, no wonder it is difficult to grasp for people just learning the language.

In my side projects I completely avoid its use, but in a real job you need to reach an agreement with the rest of the team if you want the codebase to be as consistent as possible.

Although `unless` is vastly used in the Ruby community, surprisingly I did not find much literature about it, with [some exception](https://signalvnoise.com/posts/2699-making-sense-with-rubys-unless). I am not taking into account the articles about the basic structures of the language, of course.

I would like to highlight that the author of the post linked above considers following code "little gems":

```ruby
i += 1 unless i > 10

unless person.present?
  puts "There's no such person"
end
```

Once again, I wonder if [Ruby](https://en.wikipedia.org/wiki/Ruby_\(programming_language\)) was designed with an emphasis on programming productivity and simplicity, why complicate things?

I would write the same code as follows:

```ruby
i += 1 if i <= 10

if person.blank?
  puts "There's no such person"
end
```

Much easier, right? To me it feels more natural and easier to understand. By far.

Whilst I disagree with his examples, I agree with his rules of thumb:

* Avoid using more than a single logical condition.
* Avoid negation as `unless` is already negative.
* Never use an `else` clause with an `unless` statement.

Next I will show some real-life examples with `unless` along with the code I would write instead.

Probably the most common example is the use of the truthy value of a given variable:

```ruby
return unless user
```

Nobody will ever convince me that previous line is more readable that the following one:

```ruby
return if user.nil?
```

Other common example is with collections:

```ruby
return unless posts.any?

# ...instead of...

return if posts.empty?
```

Another common example is with equality:

```ruby
return unless currency == "EUR"

# ...instead of...

return if currency != "EUR"
```

Certainly there is a lot of personal preference here. What is readable for you may not be readable for me. And the other way around.

A good example could be the next one:

```ruby
return unless user.confirmed?

# ...instead of...

return if !user.confirmed?
```

Personally, I prefer negating the condition in that case, but I think we should always stick to whatever the team decides.

I would even prefer the use of `not` if what we are looking for is readability. Although I have never done that because the [Ruby style guide](https://rubystyle.guide/#bang-not-not) has always discouraged it.

Yet another example would be the use of `unless` along with `else`:

```ruby
unless user_signed_in?
  render "login"
  render "signup"
else
  render "profile"
  render "logout"
end

# ...instead of...

if user_signed_in?
  render "profile"
  render "logout"
else
  render "login"
  render "signup"
end
```

Fortunately, it is not so common to find such code.

The one rule I always follow, mentioned above, is to never use more than one logical condition with `unless`.

```ruby
def user_has_access_to_course?
  return false unless user.present? && course.present?

  user.purchased_course?(course)
end

# ...instead of...

def user_has_access_to_course?
  return false if user.blank? || course.blank?

  user.purchased_course?(course)
end
```

My brain is about to explode every time I run into that kind of conditions.

I also prefer to see any conditional upfront, so I try to avoid **conditional modifiers** as much as possible. Those conditionals are placed after another statement allowing to run the code only when the condition is met.

The obvious exception to that rule are **guard clauses**, mainly used to avoid a condition wrapping the whole body of a method:

```ruby
def url
  if course
    course_url(course)
  end
end

# ...or...

def url
  course_url(course) if course
end

# ...but preferably...

def url
  return if course.nil?

  course_url(course)
end
```

Keep in mind that the code before the condition is simple in those examples, but I have found on countless ocassions a conditional at the end of a line with a length of 100 characters or more. That is not exactly what I would call readability.

Besides that when there are two possible paths in the code, I do not care if the code is written with a full `if`/`else` or a guard clause:

```ruby
def url
  if current_user.nil?
    public_course_url(course)
  else
    private_course_url(course)
  end
end

# ...alternatively...

def url
  return public_course_url(course) if current_user.nil?

  private_course_url(course)
end
```

But usually my teammates have prefered the guard clause, and I am ok with that option, as long as we are consistent.

As I said before, you have to reach an agreement with your team about how to write the code. In my opinion, a linter is the best possible tool to enforce those agreements.

Every single team I have been part of have always chosen [RuboCop](https://github.com/rubocop/rubocop) as linter. Out of the box it will enforce many of the guidelines outlined in the community [Ruby Style Guide](https://rubystyle.guide/).

Next, I would like to highlight some cops that are related to conditionals:

* [Style/IfUnlessModifier](https://docs.rubocop.org/rubocop/cops_style.html#styleifunlessmodifier): Checks for `if` and `unless` statements that would fit on one line if written as modifier and checks for conditional modifiers lines that exceed the maximum line length. I always disable it.
* [Style/NegatedIf](https://docs.rubocop.org/rubocop/cops_style.html#stylenegatedif): Checks for uses of `if` with a negated condition. Only `if`s without `else` are considered. I always disable it.
* [Style/InvertibleUnlessCondition](https://docs.rubocop.org/rubocop/cops_style.html#styleinvertibleunlesscondition): Checks for usages of `unless` which can be replaced by `if` with inverted condition. I always enable it.
* [Style/NegatedUnless](https://docs.rubocop.org/rubocop/cops_style.html#stylenegatedunless): Checks for uses of `unless` with a negated condition. Only `unless` without `else` are considered. I always enable it.
* [Style/UnlessElse](https://docs.rubocop.org/rubocop/cops_style.html#styleunlesselse): Looks for `unless` expressions with `else` clauses. I always enable it.
* [Style/IfUnlessModifierOfIfUnless](https://docs.rubocop.org/rubocop/cops_style.html#styleifunlessmodifierofifunless): Checks for `if` and `unless` statements used as modifiers of other conditional statements. I always enable it.

The related rules from the style guide are the following:

* [if as a modifier](https://rubystyle.guide/#if-as-a-modifier)
* [if vs unless](https://rubystyle.guide/#unless-for-negatives)
* [Using else with unless](https://rubystyle.guide/#no-else-with-unless)
* [Multi-line if modifiers](https://github.com/rubocop/ruby-style-guide#multi-line-if-modifiers)
* [Nested modifiers](https://rubystyle.guide/#no-nested-modifiers)

Hopefully I have given to you some arguments why `unless` is not the best option to create readable code, but that is something very subjective, so the take away is to write conditionals in a consistent way, preferably following a style guide defined by the community or your own team.

Thank you for reading and see you in the next one!
