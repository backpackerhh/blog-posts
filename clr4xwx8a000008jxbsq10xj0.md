---
title: "The one with a password manager: mobile apps"
seoDescription: "Discover the benefits of using Password Store & OpenKeychain, along with your password manager to keep your data safe in your mobile devices."
datePublished: Mon Jan 08 2024 13:09:57 GMT+0000 (Coordinated Universal Time)
cuid: clr4xwx8a000008jxbsq10xj0
slug: the-one-with-a-password-manager-mobile-apps
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1695899612465/5636a47d-bda1-4bb4-b2c9-fbd09c0357a6.png
tags: mobile-apps, github, git, android, passwords, cryptography, password-manager, gpg

---

In previous posts of this series I talked about [pass](https://blog.davidmontesdeoca.es/the-one-with-a-password-manager-pass) and [Browserpass](https://blog.davidmontesdeoca.es/the-one-with-a-password-manager-browserpass), the desktop utility and the browser extension I use to manage my passwords in Ubuntu Mate 22.04. I recommend you to first read those posts in case you are unfamiliar with the concept of *password manager* or *password store*.

Today I'd like to talk about [Password Store](https://passwordstore.app/), an Android client for [pass](https://www.passwordstore.org/) that aims to implement most of its capabilities; and [OpenKeychain](https://www.openkeychain.org/), an [OpenPGP](https://www.openpgp.org/) implementation for Android.

> In case you're an iOS user, definitely this is not the post you're looking for, but the good news is that [passfarios](https://mssun.github.io/passforios/) gets you covered as well.

Now let me introduce you to both apps.

## Password Store

Official [binary releases](https://docs.passwordstore.app/docs/users/release-channels/) are available through 4 different channels, each serving their own purpose.

[Play Store](https://play.google.com/store/apps/details?id=dev.msfjarvis.aps) and [GitHub Releases](https://github.com/Android-Password-Store/Android-Password-Store/releases) always contain the latest stable release. At the moment of writing the latest version is [1.13.5](https://github.com/android-password-store/Android-Password-Store/releases/tag/v1.13.5), released on July 28, 2021.

[F-Droid](https://f-droid.org/en/packages/dev.msfjarvis.aps/) is a FOSS-only store (Free and Open-Source Software) that takes the open source code and generates their own builds from it. F-Droid usually lags behind primary release channels, and a subset of functionality might be missing due to the requirement that binaries only contain FOSS code.

GitHub Releases contains both the `free` and `nonFree` variants, of which the `nonFree` variant then gets uploaded to the Play Store and the `free` variant to F-Droid. More details in the page about [build types](https://docs.passwordstore.app/docs/users/build-types).

[Snapshot builds](https://github.com/android-password-store/Android-Password-Store/releases/tag/latest) are generated on each push to the development branch and may contain unfinished and broken features, or more often, early access to bugfixes. These also ship with additional debugging code that simplify reporting of issues.

The project is in the process of rewriting the documentation from scratch. The work-in-progress state is available [here](https://docs.passwordstore.app/docs/intro) and the old documentation is available in the [wiki](https://github.com/android-password-store/Android-Password-Store/wiki/).

[GitHub Discussions](https://github.com/android-password-store/Android-Password-Store/discussions) can be used in case you don't understand something, or want to discuss a feature request in more detail with all community members before pitching it to maintainers.

Some highlights:

* [Autofill](https://docs.passwordstore.app/docs/users/autofill) on Android 8 and above.
    
* Access to the password store protected with fingerprint.
    
* Passwords copied to the clipboard for 45 seconds (by default, time is configurable).
    
* In-app SSH key generation to clone, pull changes from and push changes to the password store repository.
    
* Full management of secure passwords within the app.
    

## OpenKeychain

It's a [free software](https://github.com/open-keychain/open-keychain) based on the well established OpenPGP standard making encryption compatible across devices and systems.

The app is available through 2 different channels: [Play Store](https://play.google.com/store/apps/details?id=org.sufficientlysecure.keychain) and [F-Droid](https://f-droid.org/en/packages/org.sufficientlysecure.keychain/). At the moment of writing the latest version is [5.8.2](https://github.com/open-keychain/open-keychain/releases/tag/v5.8.2), released on January 7, 2023.

However, the project is in [maintenance mode](https://github.com/open-keychain/open-keychain/commit/6f38af15828e07f0186109a89b7aa57f54101cfa) since August 2021:

> WARNING: This software is no longer actively developed. We will still apply security fixes where reported, and do basic maintenance work, but no new features or will be worked on. We will try to consider and merge contributions where possible.

Most documentation is available in the [wiki](https://github.com/open-keychain/open-keychain/wiki) and other useful information is available in the [FAQ page](https://www.openkeychain.org/faq/) on their website. Among other interesting stuff, you could find information about [security](https://www.openkeychain.org/faq/#are-my-secret-keys-safe-on-my-mobile-device) or how to [import a backup with GPG](https://www.openkeychain.org/faq/#how-to-import-an-openkeychain-backup-with-gpg).

The [help page](https://www.openkeychain.org/help/) can be used in case you want to get in touch with the their community.

## Configuration

I'll show you here only the configuration required to make both apps work together.

The first time you start Password store, you will see following screen:

![Android Password Store app homepage](https://github.com/backpackerhh/blog-posts/assets/685978/40485638-1291-4707-bf23-8c635b4008bd align="left")

Press "Let's go" button to start configuring the app:

![Select repository type](https://github.com/backpackerhh/blog-posts/assets/685978/921c523f-81e1-41b2-b979-1fa9adf1c115 align="left")

In my case, I've a remote repository on GitHub, so I choose "Clone remote repo".

Fill out the repository information with the remote URL, the branch and the authentication mode you prefer:

![Repository information](https://github.com/backpackerhh/blog-posts/assets/685978/7629f46b-0295-46e5-9a12-3124c11258e3 align="left")

If you try to save now without having an SSH key created, you'll see following error:

![No SSH key error message](https://github.com/backpackerhh/blog-posts/assets/685978/7dcde693-495e-45f0-9cc4-919d764a1bfe align="left")

You can either import an existing SSH key or generate one for this purpose. I chose the latter option:

![Generate SSH Key](https://github.com/backpackerhh/blog-posts/assets/685978/46046109-3cd3-4ebc-b4f3-450d149e7856 align="left")

In the [project wiki](https://github.com/android-password-store/Android-Password-Store/wiki/Generate-SSH-Key) there is more info about generating SSH keys.

Choose the desired type and press "Generate". The public key will be then displayed:

![Public SSH key generated](https://github.com/backpackerhh/blog-posts/assets/685978/bd8369a0-2dce-412d-bde1-0199a4e0ff1b align="left")

If you try to save now without storing the SSH key on GitHub (or you Git server of choice), you'll be asked for the repository credentials:

![Asking for Git server credentials](https://github.com/backpackerhh/blog-posts/assets/685978/b7cef98b-3260-4d80-aa7c-0b3df51d7c3f align="left")

Obviously, that's not what we want, so the key must be stored on GitHub.

Go to `https://github.com/<username>/<repository>/settings/keys`:

![List of deploy keys for remote repository on GitHub (currently none)](https://github.com/backpackerhh/blog-posts/assets/685978/73684eda-e12c-4a23-8e6c-195a20eecddc align="left")

Go to `https://github.com/<username>/<repository>/settings/keys/new` ("Add deploy key" button) to add the SSH key:

![Add SSH key on GitHub](https://github.com/backpackerhh/blog-posts/assets/685978/eae29ae3-b8e1-4c12-89a4-4fb768e04720 align="left")

I only use the app to read those secure passwords, so I don't check "Allow write access". Being so, bear in mind that in the app you will only be able to pull changes from the repo.

![SSH key added to GitHub](https://github.com/backpackerhh/blog-posts/assets/685978/d55d0cfe-cc7d-4d07-8ef8-f7afe2685d34 align="left")

Go back to the app and press "Clone" button:

![Cloning remote repository](https://github.com/backpackerhh/blog-posts/assets/685978/d27e9d71-8995-4810-a439-1932eb05c790 align="left")

The result after the repository is successfully cloned:

![Password store cloned from GitHub](https://github.com/backpackerhh/blog-posts/assets/685978/3d39d3ab-c77a-4fc3-a47a-0972641e4e2d align="left")

Assuming you don't have OpenKeychain already installed, if you try to get any given password will result in following error:

![OpenKeychain app missing error message](https://github.com/backpackerhh/blog-posts/assets/685978/24695113-d835-4dbb-8d5d-e6ea1c39dedc align="left")

Once the app is installed, if you try again you'll see following dialog:

![Allow access to OpenKeychain dialog](https://github.com/backpackerhh/blog-posts/assets/685978/c77207e7-a187-4edf-8c30-abdcfee53dba align="left")

Without any GPG key in OpenKeychain yet, following error will appear:

> Error from OpenKeychain: No encrypted data with known secret key found in stream!

Check [details](https://github.com/android-password-store/Android-Password-Store/wiki/Your-GPG-key) provided by Password Store about exporting a GPG key.

Next switch to OpenKeychain app:

![OpenKeychain app homepage](https://github.com/backpackerhh/blog-posts/assets/685978/2edf751f-3a60-478b-9cfc-bd398a1a808a align="left")

Select "Import key from file" option:

![Import keys](https://github.com/backpackerhh/blog-posts/assets/685978/05c2e02f-070c-4add-960f-7df84938d2a8 align="left")

You can import the GPG key from the clipboard or from a file present in the device.

Right before importing the key, you'll see details about that key:

![Details about the GPG key before importing it](https://github.com/backpackerhh/blog-posts/assets/685978/f5665342-0266-4203-a22d-a4610502a1a2 align="left")

Once the key is imported you'll see it listed in the app:

![GPG key just imported](https://github.com/backpackerhh/blog-posts/assets/685978/01008545-a987-4f72-bd5d-89ecf1fbe5d7 align="left")

Go back to Password Store and you'll see the following dialog if you try to get any given password:

![Allow Password Store access to OpenKeychain key dialog](https://github.com/backpackerhh/blog-posts/assets/685978/b3d4b4e5-ee28-4d5d-b1d1-3d2799ebf149 align="left")

Allow access and you'll be asked about GPG key password:

![Enter GPG key password](https://github.com/backpackerhh/blog-posts/assets/685978/55e8ad72-ff00-4e32-991f-bdf08a150193 align="left")

The GPG key password will be remembered until cleared by default, but there are other options available as well:

![Available options to remember GPG key password](https://github.com/backpackerhh/blog-posts/assets/685978/74e33caf-186a-49ce-8d0c-98eb7cdf92bd align="left")

At this point your password will be available in the clipboard for 45 seconds (default) and a notification will appear in your device:

![OpenKeychain saved passwords notification](https://github.com/backpackerhh/blog-posts/assets/685978/a2e4bd40-d9e6-4ddf-a6ed-2ab7d7dda368 align="left")

> It's worth noting that if you left the default option when you entered the GPG key password, until you specifically clear the password you'll be able to access to any saved password without be asked again for it. Sometimes that's what you want, but sometimes it's not.

Let's improve the UX enabling the autofill feature.

At the top right of the main screen, select the three dots:

![Main menu in Password Store app](https://github.com/backpackerhh/blog-posts/assets/685978/23dff702-2167-45e6-8324-90a52669249f align="left")

Go to settings:

![Settings options in Password Store app](https://github.com/backpackerhh/blog-posts/assets/685978/768e9013-4afc-4ac3-9455-a103c3e31d21 align="left")

Enable autofill feature:

![Enable autofill dialog](https://github.com/backpackerhh/blog-posts/assets/685978/a51551e8-1aea-4667-937c-a1239172ae68 align="left")

Configure autofill service in Android:

![Autofill service in Android](https://github.com/backpackerhh/blog-posts/assets/685978/9f26c568-e4b6-462f-ac9f-e7521369a3b6 align="left")

Select Password Store from the list and confirm that you trust the app:

![Password Store as autofill service confirmation dialog](https://github.com/backpackerhh/blog-posts/assets/685978/b5019de8-e03f-4def-8a95-609d5e0d14ab align="left")

New options appear in the settings menu once autofill is enabled:

![Autofill settings options](https://github.com/backpackerhh/blog-posts/assets/685978/187cf8b2-c85d-48ee-95b2-8299d19a36db align="left")

Example with any Android app, in this case Goodreads:

![Goodreads with autofill options](https://github.com/backpackerhh/blog-posts/assets/685978/8f38ef1f-790f-4037-ab1d-db55c38a1395 align="left")

Search in store for "Goodreads":

![Search Goodreads in password store](https://github.com/backpackerhh/blog-posts/assets/685978/00b0df79-de40-474e-b394-df733b8cc2e1 align="left")

Credentials are automatically filled once selected:

![Goodreads credentials filled once selected](https://github.com/backpackerhh/blog-posts/assets/685978/03efafaa-fe37-46fa-943e-79308289ebd3 align="left")

Next time, the password previously used is displayed at the top:

![Display password previously used at the top](https://github.com/backpackerhh/blog-posts/assets/685978/d8b379df-5922-4930-909b-3b127b7a0a17 align="left")

Example with any website, in this case GitHub:

![GitHub with autofill options](https://github.com/backpackerhh/blog-posts/assets/685978/cb0b8198-720f-4afa-b7a5-a7abc6634080 align="left")

And that's it. Both apps are correctly configured and working as one could expect.

### Other options

If you recall, the SSH key didn't have write permissions, so you won't be able to push changes to the remote repo:

![Error during Git operation dialog](https://github.com/backpackerhh/blog-posts/assets/685978/caca1f88-6aa1-4a3d-a256-977af2a3e588 align="left")

You can edit Git server settings:

![Edit Git server settings](https://github.com/backpackerhh/blog-posts/assets/685978/3cab23be-3b21-42eb-a18f-ba35393f864b align="left")

For more security accessing the password store, enable biometric authentication:

![Enable biometric authentication](https://github.com/backpackerhh/blog-posts/assets/685978/adad4537-d877-46ed-a43c-c4cd1fd3f816 align="left")

In case you want to generate passwords from the app, you could select the generator type:

![Password generator type](https://github.com/backpackerhh/blog-posts/assets/685978/5a1a95d0-98de-4aed-99af-9edf26f156fd align="left")

You could organize passwords as desired:

![Create folders and passwords from Password Store app](https://github.com/backpackerhh/blog-posts/assets/685978/fb13cf5f-8ef4-4a45-81e9-136100fe0c57 align="left")

Main menu in OpenKeychain:

![OpenKeychain main menu](https://github.com/backpackerhh/blog-posts/assets/685978/22e24910-3deb-4a20-aec9-766a9b975d94 align="left")

Encrypt and decrypt files and text:

![Encrypt and decrypt options](https://github.com/backpackerhh/blog-posts/assets/685978/dd7ccb2b-375c-4aa1-bf72-40ae5d39c19e align="left")

Supported apps:

![OpenKeychain supported apps](https://github.com/backpackerhh/blog-posts/assets/685978/6ec2b7c2-1b5c-45e6-b3a5-e7b8af852a77 align="left")

Backup and restore:

![OpenKeychain backup and restore](https://github.com/backpackerhh/blog-posts/assets/685978/142eb72c-e74a-4454-850d-a27152c9bdf3 align="left")

Other settings:

![Other OpenKeychain settings](https://github.com/backpackerhh/blog-posts/assets/685978/47ff8238-567c-482e-a120-5cd3df09a8d1 align="left")

Some information about any of your GPG keys is available:

![OpenKeychain GPG key status](https://github.com/backpackerhh/blog-posts/assets/685978/fe02c635-4536-4b55-8525-7483c6c259ea align="left")

You could change the GPG key password or create a backup, for instance:

![Menu options from the GPG key](https://github.com/backpackerhh/blog-posts/assets/685978/309e64ec-10ad-4265-88f8-e1e58d98bff6 align="left")

New GPG keys can be added in different ways:

![Add new GPG keys to OpenKeychain](https://github.com/backpackerhh/blog-posts/assets/685978/42bc2ddf-fdea-48f7-bbc4-d91f35e68e20 align="left")

Manage or update GPG keys:

![Manage GPG keys](https://github.com/backpackerhh/blog-posts/assets/685978/e518c785-1f04-4b6e-a94b-8c589135b777 align="left")

Advanced settings for a GPG key:

![Advanced settings for a GPG key](https://github.com/backpackerhh/blog-posts/assets/685978/dace027a-68a9-462f-ad15-fe8c070d34c9 align="left")

## Conclusion

I highly value the functionality provided by these apps, although I wish I didn't have to install 2 different apps for that. For a long time the maintainers of both projects have considered the possibility of [making OpenKeychain a library rather than a separate app](https://github.com/android-password-store/Android-Password-Store/issues/1195), but no progress have been made, let alone now that the project is in maintenance mode.

A new version of Password Store app has not been released in over 2 years, but taking a look at the activity in issues and pull requests merged the project seems to be active yet.

Either way, it's quite easy to configure both apps and making them work together. Not to mention that I haven't found any critical error so far, at least with the basic use I make of them.

Thank you for reading and see you in the next one!