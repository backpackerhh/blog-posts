---
title: "The one about accepting Tab suggestions in Cursor"
seoDescription: "Learn how to resolve issues with accepting AI-generated suggestions in Cursor using the Tab key by adjusting keyboard shortcuts effectively"
datePublished: Mon Aug 25 2025 18:29:19 GMT+0000 (Coordinated Universal Time)
cuid: cmergbhr5000802jmghsye75s
slug: the-one-about-accepting-tab-suggestions-in-cursor
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1756143334985/5b51dc25-e807-44b8-93ad-38116dff3f2d.png
tags: ai, macos, vs-code, cursor, cursor-ide, cursor-ai

---

In the [previous post](https://blog.davidmp.es/the-one-about-shortcuts-in-cursor-with-karabiner-elements), I mentioned that I was starting to use Cursor as my main IDE, after having worked with VSCode and Copilot for a while.

A key advantage of Cursor for VSCode users is its support for the same keyboard shortcuts, allowing for a seamless transition:

![Cursor's Quick Start](https://github.com/user-attachments/assets/cf849e77-a2b8-47c8-8160-9d3c73d0d0d1 align="left")

It allowed me to be really productive with this IDE almost immediately. And I say almost, because I ran into some issues when trying to accept AI suggestions on macOS.

Cursor allows you to autocomplete with [Tab](https://docs.cursor.com/en/tab/overview), the model they have trained in-house. With Tab, you can autocomplete multiple lines and blocks of code and jump in and across files to the next autocomplete suggestion.

As an example, I am going to show what happens when I try to accept a suggestion by adding a simple `console.log` to a JS file:

![User cannot accept suggestions with tab key in Cursor](https://github.com/user-attachments/assets/effbc211-2d1a-4c66-8976-24fc468a5e50 align="left")

You can see that I can only accept a suggestion if I place the cursor over the suggestion itself and click *Accept*.

I dealt with this issue for longer than I would like to admit before I finally had time to investigate the root cause.

When I was able to look into the problem, I started with a quick Google search, where I did not find a [real solution](https://www.reddit.com/r/cursor/comments/1juzs0d/does_anyone_elses_cursor_tab_autocomplete_get_in/). Actually, that was a good sign, because it probably meant it was due to a misconfiguration on my end.

So, I continued by visually searching for the keyboard shortcuts associated with the `tab` key.

To do this, you have to go to the command palette (`control + shift + p`) and type *keyboard shortcuts*. There, you choose the option that does not specify *JSON*.

Then, you have to type `"tab"`, with the quotes, or on the right side of the search bar, click `Record Keys` and press the `tab` key.

![Cursor's Keyboard Shortcuts](https://github.com/user-attachments/assets/3841c8e5-3a5d-4417-8239-c1c44066f1d0 align="left")

The first detail you might note is that the source for almost all the commands is *User*. This is because I do not like working with macOS, but I have to for work, so I configured the system to be as similar as possible to my personal laptop with Linux. I have a [post](https://blog.davidmp.es/the-one-about-working-with-macos-being-a-linux-user#heading-vscode) where I discussed this topic in more detail.

Another important detail to note is the first command associated with this key and its [when clause context](https://code.visualstudio.com/api/references/when-clause-contexts): `cpp.shouldAcceptTab`. We will need to use it pretty soon.

Next, we go back to the command palette and type *keyboard shortcuts* again. This time, we choose the option that specifies *JSON*.

Here we have to search for the commands associated with the `tab` key and disable them one by one until we find the one that is interfering with the expected behavior in Cursor. I started with the most obvious choice:

```json
{
  "key": "tab",
  "command": "tab",
  "when": "editorTextFocus && !editorReadonly && !editorTabMovesFocus"
}
```

To test my theory, I commented out the entire entry for that shortcut:

![Shortcut disabled in Cursor to validate default behavior](https://github.com/user-attachments/assets/57fe0a4a-2984-401a-9c46-5bad9de061c5 align="left")

As expected, with that command disabled, I can now accept Tab's suggestions. However, I noticed an strange behavior when indenting code, for example.

![Weird behavior found while trying to indent code with tab key in Cursor](https://github.com/user-attachments/assets/4d45787b-fd8a-47c7-a1a8-8672fc767d3c align="left")

So, I added the condition we saw earlier, negated, to this command's `when` clause:

```json
{
  "key": "tab",
  "command": "tab",
  "when": "!cpp.shouldAcceptTab && editorTextFocus && !editorReadonly && !editorTabMovesFocus"
}
```

Now I can accept Tab's suggestions without interfering with the default behavior of the `tab` key.

![Weird behavior found regarding indentation in the code with tab key in Cursor is now solved](https://github.com/user-attachments/assets/4361b086-3dfd-4c45-9db4-d21ab10992ee align="left")

Once again, there was one small detail that still was not working as I expected:

![User not always can accept suggestions with tab key in Cursor](https://github.com/user-attachments/assets/3dccdbf5-968d-4cdd-b372-49e56cba50bc align="left")

When I had a Tab suggestion and other suggestions from the current context, pressing the `tab` key accepted the first suggestion from the context, and only when I pressed the `tab` key again did it accept the expected suggestion.

To find the command that was interfering with this behavior, I disabled them one by one until I found the one:

```json
{
  "key": "tab",
  "command": "acceptSelectedSuggestion",
  "when": "suggestWidgetHasFocusedSuggestion && suggestWidgetVisible && textInputFocus"
}
```

> Remember to re-enable each command after you have confirmed it is not the one you were looking for.

I added the same negated condition to this `when` clause:

```json
{
  "key": "tab",
  "command": "acceptSelectedSuggestion",
  "when": "!cpp.shouldAcceptTab && suggestWidgetHasFocusedSuggestion && suggestWidgetVisible && textInputFocus"
}
```

The result is now exactly what I expected:

![User can accept suggestions with tab key in Cursor](https://github.com/user-attachments/assets/54167738-de7b-476a-b667-9b34010a2a3b align="left")

In this case, the standard behavior of the `tab` key was clashing with Cursor's AI suggestion feature. The solution was to only trigger the default *tab* and *acceptSelectedSuggestion* commands when the AI does not have a suggestion ready.

The key takeaway is not just about this specific fix, but about the power of `when` clauses for fine-tuning the IDE to behave exactly how you want it to. Hopefully, this guide saves you some time and helps you get back to coding faster.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]