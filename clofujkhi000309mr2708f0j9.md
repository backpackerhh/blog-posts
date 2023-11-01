---
title: "The one with a password manager: Browserpass"
seoDescription: "Discover the benefits of using Browserpass, a simple browser extension, along with your password manager to keep your data safe in Ubuntu and Chrome."
datePublished: Wed Nov 01 2023 14:21:56 GMT+0000 (Coordinated Universal Time)
cuid: clofujkhi000309mr2708f0j9
slug: the-one-with-a-password-manager-browserpass
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1694701929855/b8003002-0811-4581-9cfe-d9fda8dfea49.png
tags: ubuntu, linux, chrome, chrome-extension, password-manager

---

In the [previous post of this series](https://blog.davidmontesdeoca.es/the-one-with-a-password-manager-pass) I talked about **pass**, the desktop utility I use in Ubuntu Mate 22.04 as *password manager*. I recommend you to read that post first in case you are unfamiliar with the concept of *password manager* or *password store*.

Today I'd like to talk about [Browserpass](https://github.com/browserpass/browserpass-extension), a browser extension that allows you to auto-fill or copy to clipboard credentials for the current domain, protecting you from phishing attacks.

## Installation

In my case, my browser of choice is Chrome, so I can install the extension from [Chrome Web Store](https://chrome.google.com/webstore/detail/browserpass/naepdomgkenhinolocfifgehidddafch), which will provide auto-updates.

For other browsers and/or operating systems, check [installation details](https://github.com/browserpass/browserpass-extension#installation).

In order to use **Browserpass** you must also install a [companion native messaging host](https://github.com/browserpass/browserpass-native), which provides an interface to the password store.

The package [webext-browserpass](https://packages.debian.org/sid/webext-browserpass) available for Debian-based distributions is [reported](https://github.com/browserpass/browserpass-native/issues/126) to work only for Chromium and Firefox, so I had to install the host app manually.

Install the dependencies that you will need to build the app locally:

```bash
$ sudo apt install golang-go curl
```

Go to the [releases page](https://github.com/browserpass/browserpass-native/releases), scroll down to the *Assets* section, click on "*Show all {n} assets*" and download the file you need. Choose either the source code file, if you want to compile the app yourself, or a file for your operating system containing a pre-built binary. In my case, I downloaded the *.tar.gz* file for a Linux system with a 64-bit processor.

I highly recommend you to verify the signature of that file using GPG. Follow [these instructions](https://github.com/browserpass/browserpass-native#install-manually).

After having verified the downloaded file, uncompress it:

```bash
$ cd ~/Downloads
$ tar -xf browserpass-linux64-{latest version}.tar.gz
```

Access to the container directory and run following command to configure the host app:

```bash
$ cd browserpass-linux64-{latest version}
$ make BIN=browserpass-linux64 configure
/usr/bin/sed -i 's|"path": ".*"|"path": "'"/usr/bin/browserpass-linux64"'"|' browser-files/chromium-host.json
/usr/bin/sed -i 's|"path": ".*"|"path": "'"/usr/bin/browserpass-linux64"'"|' browser-files/firefox-host.json
```

To install the host app you'll need root privileges:

```bash
$ sudo make BIN=browserpass-linux64 install
[sudo] password for david:
/usr/bin/install -Dm755 -t "/usr/bin/" browserpass-linux64
/usr/bin/install -Dm644 -t "/usr/lib/browserpass/" Makefile
/usr/bin/install -Dm644 -t "/usr/share/licenses/browserpass/" LICENSE
/usr/bin/install -Dm644 -t "/usr/share/doc/browserpass/" README.md
/usr/bin/install -Dm644 browser-files/chromium-host.json   "/usr/lib/browserpass/hosts/chromium/com.github.browserpass.native.json"
/usr/bin/install -Dm644 browser-files/chromium-policy.json "/usr/lib/browserpass/policies/chromium/com.github.browserpass.native.json"
/usr/bin/install -Dm644 browser-files/firefox-host.json    "/usr/lib/browserpass/hosts/firefox/com.github.browserpass.native.json"
```

And that's it.

> Bear in mind that it's expected that you keep both components up to date, as most improvements require changing code in both browser extensions and the host application.

## Configuration

Pin the extension in the browser:

![Browserpass extension pinned](https://user-images.githubusercontent.com/685978/270672287-a0752f76-fa34-49ab-a832-cd7ed009430a.png align="left")

Right click on the **Browserpass** icon:

![Browserpass extension menu](https://user-images.githubusercontent.com/685978/270672284-95b0e979-8b3c-423a-a1e7-764a4292b1b4.png align="left")

You'll find a few configurable settings, such as the location of the GPG key or the location of the password store:

![Browserpass extension options](https://user-images.githubusercontent.com/685978/270672277-6b707287-95e5-4616-bb43-35683f85f6e7.png align="left")

Besides, you can choose between the dark and the light theme too:

![Browserpass extension dark theme](https://user-images.githubusercontent.com/685978/279340065-abcec445-1474-468a-aeb1-a2409ed1fc79.png align="left")

![Browserpass extension light theme](https://user-images.githubusercontent.com/685978/279340068-dc67a214-cfa2-4777-a68c-b3c7ab8db055.png align="left")

Check the [project page](https://github.com/browserpass/browserpass-extension#options) for more details about all the options available to configure this extension.

## Usage

### Conventions

**Browserpass** recommends following some conventions to organize the password store.

One such convention would be that in order to benefit of **phishing attack protection**, a password entry file, or any of its parent folders, must contain a full domain name (including [TLD](https://en.wikipedia.org/wiki/Top-level_domain) like `.com`) and optionally port in their name in order to automatically match a website. Although entries which don't contain such a domain in their name may still be manually selected.

Some good examples:

```plaintext
$ pass
Password Store
├── accounts.google.com
├── amazon.com
└── github.com
    ├── personal
    └── work
```

In the [project page](https://github.com/browserpass/browserpass-extension#usage) you'll find more conventions expected by this extension.

### Shortcuts

Click on the extension icon or use `Ctrl + Shift + L` to open the **Browserpass** popup with the entries that match the current domain.

You can also use `Ctrl + Shift + F` to fill the form with the best matching credentials without even opening the popup. The best matching credentials are the first ones on the list if you open the popup.

Those shortcuts are configurable. In Chrome, you can go to `chrome://extensions/shortcuts`:

![Browserpass configurable shortcuts](https://user-images.githubusercontent.com/685978/270921529-894a7997-0532-4df9-8aa3-d72b2bcb8419.png align="left")

This extension provides the following keyword shortcuts:

| Shortcut | Action |
| --- | --- |
| `Ctrl + Shift + L` | Open **Browserpass** popup |
| `Ctrl + Shift + F` | Fill the form with the best matching credentials |
| `Enter` | Submit form with currently selected credentials |
| Arrow keys and `Tab` / `Shift + Tab` | Navigate popup list |
| `Ctrl + C` | Copy password to clipboard (will clear in 60 seconds) |
| `Ctrl + Shift + C` | Copy username to clipboard (will clear in 60 seconds) |
| `Ctrl + G` | Open URL in the current tab |
| `Ctrl + Shift + G` | Open URL in the new tab |
| `Backspace` (with no search text entered) | Search passwords in the entire password store |

> Note that if the cursor is located in the search input field, every shortcut that works on the selected entry will be applied on the first entry in the popup list.

When **Browserpass** shows entries for a specific domain, either because it matches the domain or because you have used it before for that domain, you will see a badge with the domain name in the search input field:

![Browserpass badge with the domain name](https://user-images.githubusercontent.com/685978/279340065-abcec445-1474-468a-aeb1-a2409ed1fc79.png align="left")

That means that phishing attack protection is enabled, but if you want to intentionally disable it and search the entire password store for credentials, you must press `Backspace` to confirm this decision (domain badge will disappear), then use **Browserpass** normally.

![Browserpass showing previously selected credentials for a given domain although the domain doesn't match](https://user-images.githubusercontent.com/685978/279340090-490ad39f-fbae-4656-960a-b51bccf3f745.png align="left")

The selected credentials will be remembered and used the next time you go back to the same domain.

Go to the [project page](https://github.com/browserpass/browserpass-extension#password-matching-and-sorting) once again to find more about password matching and sorting.

## Latest release

At the moment of writing, the latest version released is 3.8.0, that brings [a long-awaited functionality](https://github.com/browserpass/browserpass-extension/issues/61), allowing users to manage the password store directly from the browser extension.

Although I personally won't use that functionality, it's definitely something worth mentioning.

A new option has been added to the bottom of the popup:

![Browserpass allows to add new credentials](https://github.com/backpackerhh/blog-posts/assets/685978/c1a04a76-abbf-4b25-ac20-0502dab88f82)

In the form you can add an existing password or generate a random one with desired length and optionally including symbols:

![Browserpass dedicated form to add new credentials](https://user-images.githubusercontent.com/685978/279350608-605d7ab1-81b4-4371-b86d-15e9e30346bd.png)

You can edit existing credentials accessing from the _Open details_ option: 

![Browserpass allows to edit existing credentials](https://user-images.githubusercontent.com/685978/279340077-24107932-fe17-4443-9a31-dbf35e42dae1.png)

And then clicking on the button at the top right of the popup:

![Browserpass option to edit credentials](https://user-images.githubusercontent.com/685978/279340083-4f1d1960-6f43-4d17-a3eb-08c8e5a7481a.png)

You can either delete or update existing credentials:

![Browserpass dedicated form to delete or update credentials](https://user-images.githubusercontent.com/685978/279340085-cfe08107-b9f8-4019-9416-7841343b59a7.png)

## Security

A reasonable concern could be the [security](https://github.com/browserpass/browserpass-extension#security) provided by this extension.

As I mentioned before, it **protects against phishing** by default and it's claimed that it doesn't allow websites to trigger any extension action, without the user directly invoking it.

That being said, bear in mind that **Browserpass** doesn't attempt to secure the data it stores in browser local storage, it's assumed that users take precautions to protect their local file system (e.g. by using disk encryption).

## Conclusion

I really like the simplicity of **Browserpass**. So far I haven't needed anything more than what it offers, although I must admit that it took me a while until I finally managed to configure properly the host app. Assuming your OS is Ubuntu and your browser is Chrome, you won't have the same problem after reading the content of this post, so it should be a pretty smooth experience for you.

In the next post of this series I'll talk about the mobile apps I use in combination with **pass**.

Thank you for reading and see you in the next one!