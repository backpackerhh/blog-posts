---
title: "The one about shortcuts in Cursor with Karabiner-Elements"
seoDescription: "Learn how to configure Cursor shortcuts in Karabiner-Elements for seamless transition and enhanced productivity with AI-native IDEs"
datePublished: Wed Jul 23 2025 13:43:17 GMT+0000 (Coordinated Universal Time)
cuid: cmdg0kj3q001602la0aog5op7
slug: the-one-about-shortcuts-in-cursor-with-karabiner-elements
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1753278053674/2552475e-82db-479e-b67f-abf5761fdd8f.png
tags: ai, macos, vs-code, cursor, copilot, macos-tips, cursor-ide, karabiner-elements, osascript

---

Recently, I was assigned a spike to check the feasibility of migrating a React application to a microfrontend with [Sinatra](https://sinatrarb.com/) + [htmx](https://htmx.org/) entirely using AI. In this case, with [Claude Code](https://www.anthropic.com/claude-code).

Of course, I did not have to try to do the entire migration at once, but rather migrate a list of beneficiaries and a detail view of a specific beneficiary, following the existing design.

The main goal was to find out if it was possible to do it with AI and, if so, how long it would take to complete each functionality compared to how long I estimated it would take to do the same manually.

The client I work for gives each developer a [Copilot](https://github.com/features/copilot) license for VSCode or a license for [Cursor](https://cursor.com/). You can switch between them as many times as you want, but whenever you activate one, the other automatically expires.

In my case, I have been working with VSCode for a while, but I was offered the possibility of trying Cursor as well, to test the integration with Claude Code.

Cursor is an AI-native IDE forked from VS Code, so it allows for a very easy transition from one IDE to the other. However, I found that a few basic shortcuts did not work as expected: save, find, copy, paste, undo, etc.

The issue was directly related to the shortcuts I configured with [Karabiner-Elements](https://karabiner-elements.pqrs.org/) in macOS to behave like in Linux. I talked about it some time ago [here](https://davidmontesdeoca.dev/the-one-about-working-with-macos-being-a-linux-user#heading-karabiner-elements).

VSCode's *bundler identifier* is very easy to find anywhere if you look for it:

```
com.microsoft.VSCode
```

Cursor's bundler identifier is not as widely known. Finally, I came across [this Cursor issue](https://github.com/cursor/cursor/issues/901), which shows how to get it:

```bash
osascript -e 'id of app "Cursor"'
```

The surprise was that the value seems quite random:

```
com.todesktop.230313mzl4w4u92
```

I did not know [osascript](https://victorscholz.medium.com/what-is-osascript-e48f11b8dec6), so I thought it might not be the bundler identifier I was looking for.

To be sure, I did the same with VSCode:

```bash
osascript -e 'id of app "code"'
```

The value is as expected:

```
com.microsoft.VSCode
```

I assumed that, at least for now, that Cursor's bundle identifier would be enough. I added that value to the array of bundle identifiers of one of the shortcuts configured for VSCode in Karabiner-Elements and it worked perfectly.

Finally, I added it to the rest of the shortcuts for VSCode. You can see the complete configuration in [this gist](https://gist.github.com/backpackerhh/2448998967f178f0114de6c6a3eb37df).

In case you did not know, the [official Karabiner-Elements documentation](https://karabiner-elements.pqrs.org/docs/json/complex-modifications-manipulator-definition/conditions/frontmost-application/#investigate-the-bundle-identifier-and-file-path) includes an alternative way to get an application's bundle identifier.

In any case, I continued investigating about that unusual bundler identifier and found [this thread](https://forum.cursor.com/t/cursor-bundle-identifier/779) in the Cursor forum, where Cursor's CEO and founder confirm that this value will not change, because the application is already published.

Therefore, we can rest assured that it will not be necessary to change the configuration in the future.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]
