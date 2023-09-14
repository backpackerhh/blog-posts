---
title: "The one with a password manager: pass"
seoDescription: "Discover the benefits of using a password manager to keep your data safe in Ubuntu with pass, a simple CLI utility."
datePublished: Thu Sep 14 2023 11:51:40 GMT+0000 (Coordinated Universal Time)
cuid: clmj41ft1001908jrcdi18fkb
slug: the-one-with-a-password-manager-pass
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/FnA5pAzqhMM/upload/ee44def636e20ff6efabe239bfb38778.jpeg
tags: ubuntu, linux, git, passwords, password-manager

---

Nowadays I find it impossible not to use a password manager to keep my data secure. However, it wasn't always that way.

As we've probably all done at one time or another, I used to use the same password on multiple sites. It wasn't always the same, but all passwords included something that had some sort of meaning to me. Nothing as simple as "1234", "admin", "password" or my birthdate, but they were still weak and unsafe passwords.

With the proliferation of [data breaches](https://haveibeenpwned.com/Passwords), it was recommended that all passwords include at least one lowercase letter, one uppercase letter, a number and perhaps some symbol (.,-\_&#).

So I started using small variations of my previous passwords, making sure to include here and there some of those recommended characters, in the hope that they would be somewhat more secure. And in a way they were.

What I didn't count on was my tendency to forget passwords, especially what password I used for a particular website or application.

What did I do then? I usually reset the password by putting in one that was easier to remember. I would make the same mistake again, ignoring the lack of security.

Sometimes I would simply save them to my [Google account](https://passwords.google.com/), where my data was synchronized and wherever I logged in I would have them available.

Although that data that Google stores is supposedly encrypted, I never wanted to store particularly sensitive data such as access to bank accounts or credit cards.

Therefore, I found myself with a combination of more or less weak passwords and others that I didn't feel comfortable storing anywhere.

This is where a [password manager](https://www.techtarget.com/searchsecurity/definition/password-manager) can help. It allows you to store all your passwords securely, so you don't have to worry about remembering them, and allows you to use unique, strong passwords everywhere.

## A desktop utility

My password manager of choice in Ubuntu MATE 22.04 is [pass](https://www.passwordstore.org/), a very simple password store that keeps each password inside a **gpg2** encrypted file whose filename is the title of the website or resource that requires the password.

All those encrypted files may be organized into meaningful directory hierarchies residing at _~/.password-store_.

**pass** is a very short and simple shell script, capable of temporarily putting passwords on your clipboard and tracking password changes using _git_, providing some nice commands for adding, editing, generating, and retrieving passwords.

Check the [man page](https://git.zx2c4.com/password-store/about/) of the project repo for more info.

### Installation

```bash
$ sudo apt install pass
```

Check the [official page](https://www.passwordstore.org/#download) for other operating systems or distributions.

### Configuration

Although you could reuse an existing [GPG](https://www.gnupg.org/software/index.html) key, I highly recommend you generating a new one. Follow the instructions in [this article](https://linuxhint.com/generate-pgp-keys-gpg/).

> Don't forget the passphrase used or keep it in a safe place, as it's the only password you'll need from now on.

Check the newly created key:

```bash
$ gpg --list-secret-keys
/home/david/.gnupg/pubring.kbx
------------------------------
sec   rsa4096 2023-09-14 [SC]
      {fingerprint}
uid           [unknown] {gpg key id} <{user email}>
ssb   rsa4096 2023-09-14 [E]
```

Use the _firgerprint_ included in the output of the previous command to initialize the password store:

```bash
$ pass init {fingerprint}
mkdir: created directory '/home/david/.password-store'
Password store initialized for {fingerprint}.
```

You can additionally initialize it as a git repository:

```bash
$ pass git init
Initialized empty Git repository in /home/david/.password-store/.git/
```

If a git repository is initialized, **pass** creates a git commit each time the password store is manipulated.

Configure the remote repository so you can keep a copy of your encrypted passwords in the cloud (recommended):

```bash
$ pass git remote add origin {remote repo}
```

Check the remote repository is properly configured:

```bash
$ pass git pull
Already up to date.
```

### Usage

List all the passwords present in the store:

```bash
$ pass
Password Store
├── directory-1
│   ├── subdirectory-1
│   │   └── file-1
│   └── subdirectory-2
│       └── file-1
└── directory-2
    └── subdirectory-1
        └── file-1
```

List all passwords present in a given directory or subdirectory:

```bash
$ pass directory-1
directory-1
├── subdirectory-1
│   └── file-1
└── subdirectory-2
    └── file-1
```

Show a specific password:

```bash
$ pass directory-1/subdirectory-1/file-1
{password}
```

Copy a password to the clipboard (45 seconds by default):

```bash
$ pass -c directory-1/subdirectory-1/file-1
Copied directory-1/subdirectory-1/file-1 to clipboard. Will clear in 45 seconds.
```

Copy a password to the clipboard for as long as desired:

```bash
$ PASSWORD_STORE_CLIP_TIME=30 pass -c directory-1/subdirectory-1/file-1
Copied directory-1/subdirectory-1/file-1 to clipboard. Will clear in 30 seconds.
```

Add an existing password to the store:

```bash
$ pass insert directory-1/subdirectory-1/file-2
Enter password for directory-1/subdirectory-1/file-2:
Retype password for directory-1/subdirectory-1/file-2:
[master {git hash}] Add given password for directory-1/subdirectory-1/file-2 to store.
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 directory-1/subdirectory-1/file-2.gpg
```

Insert the password twice to save it.

Generate a new password (25 characters by default), including symbols:

```bash
$ pass generate directory-3/subdirectory-1/file-1
mkdir: created directory '/home/david/.password-store/directory-3'
mkdir: created directory '/home/david/.password-store/directory-3/subdirectory-1'
[master {git hash}] Add generated password for directory-3/subdirectory-1/file-1.
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 directory-3/subdirectory-1/file-1.gpg
The generated password for directory-3/subdirectory-1/file-1 is:
{newly generated password}
```

Generate a new password with desired length:

```bash
$ pass generate directory-3/subdirectory-1/file-2 16
[master {git hash}] Add generated password for directory-3/subdirectory-1/file-2.
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 directory-3/subdirectory-1/file-2.gpg
The generated password for directory-3/subdirectory-1/file-2 is:
{newly generated password}
```

It's possible to generate passwords with no symbols using _-n_, and copy it to the clipboard instead of displaying it at the console using _-c_.

Remove an existing password from the store:

```bash
$ pass rm directory-3/subdirectory-1/file-2
rm: remove regular file '/home/david/.password-store/directory-3/subdirectory-1/file-2.gpg'? y
removed '/home/david/.password-store/directory-3/subdirectory-1/file-2.gpg'
```

Move an existing password or directory:

```bash
$ pass mv directory-3/subdirectory-1 directory-1/subdirectory-3
/home/david/.password-store/directory-3/subdirectory-1
renamed '/home/david/.password-store/directory-3/subdirectory-1' -> '/home/david/.password-store/directory-1/subdirectory-3'
[master {git hash}] Rename directory-3/subdirectory-1 to directory-1/subdirectory-3.
 1 file changed, 0 insertions(+), 0 deletions(-)
 rename {directory-3/subdirectory-1 => directory-1/subdirectory-3}/file-1.gpg (100%)
```

If destination path ends in a trailing _/_, it is always treated as a directory.

Synchronize the remote repository with latest changes:

```bash
$ pass git push
```

The first time you'll need to set the upstream reference:

```bash
$ pass git push -u origin main
```

Note that any command provided by git is available with **pass** in the context of the password store:

```bash
$ pass git pull
$ pass git log
$ pass git status
...
```

### Export keys

If you want to keep your keys in a safe place or want to import them to another computer, you can easily export them.

Export the public key:

```bash
$ gpg --export --armor {fingerprint} > pubkey.asc
```

You won't see any output.

Export the secret (or private) key:

```bash
$ gpg --export-secret-keys --armor {fingerprint} > privkey.asc
```

You'll be asked for the passphrase before exporting the key.

Again, you won't see any output.

Check both keys were properly exported:

```bash
$ ls | egrep "*key.asc"
privkey.asc
pubkey.asc
```

### Import keys

If you want to use the password store in another computer, you can easily import the keys.

Import the public key:

```bash
$ gpg --import pubkey.asc
gpg: key {key}: public key "{gpg key id} <{user email}>" imported
gpg: Total number processed: 1
gpg: imported: 1
```

Import the secret (or private) key:

```bash
$ gpg --allow-secret-key-import --import privkey.asc
gpg: key {key}: "{gpg key id} <{user email}>" not changed
gpg: key {key}: secret key imported
gpg: Total number processed: 1
gpg: unchanged: 1
gpg: secret keys read: 1
gpg: secret keys imported: 1
```

You'll be asked for the passphrase before importing the key.

Clone the remote repository:

```bash
$ git clone {remote repo} ~/.password-store
```

At this point, while encrypting a password in the store you'll see following message:

> gpg: There is no assurance this key belongs to the named user

> gpg: [stdin]: encryption failed: Unusable public key

> Password encryption aborted.

Edit the key as specified [here](https://stackoverflow.com/questions/33361068/gnupg-there-is-no-assurance-this-key-belongs-to-the-named-user):

```bash
$ gpg --edit-key {fingerprint}
gpg (GnuPG) 2.2.27; Copyright (C) 2021 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa4096/{key}
     created: 2023-09-14  expires: never       usage: SC
     trust: unknown       validity: unknown
ssb  rsa4096/{key}
     created: 2023-09-14  expires: never       usage: E
[ unknown] (1). {gpg key id} <{user email}>

gpg> trust
sec  rsa4096/{key}
     created: 2023-09-14  expires: never       usage: SC
     trust: unknown       validity: unknown
ssb  rsa4096/{key}
     created: 2023-09-14  expires: never       usage: E
[ unknown] (1). {gpg key id} <{user email}>

Please decide how far you trust this user to correctly verify other users' keys
(by looking at passports, checking fingerprints from different sources, etc.)

  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu

Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y

sec  rsa4096/{key}
     created: 2023-09-14  expires: never       usage: SC
     trust: ultimate      validity: unknown
ssb  rsa4096/{key}
     created: 2023-09-14  expires: never       usage: E
[ unknown] (1). {gpg key id} <{user email}>
Please note that the shown key validity is not necessarily correct
unless you restart the program.

gpg> quit
```

Basically you need to ultimately trust the key, as it was created by yourself.

Check the edited key:

```bash
$ gpg --list-secret-keys
/home/david/.gnupg/pubring.kbx
------------------------------
sec   rsa4096 2023-09-14 [SC]
      {fingerprint}
uid           [ultimate] {gpg key id} <{user email}>
ssb   rsa4096 2023-09-14 [E]
```

> Note that [unknown] has been replaced with [ultimate].

You should be good now to start using the password store.

## Alternatives

There are hundreds of popular password managers, but I haven't used any other so far.
For instance, [Bitwarden](https://bitwarden.com/) has been named by [multiple](https://www.pcmag.com/picks/the-best-password-managers) [sources](https://www.cnet.com/tech/services-and-software/best-password-manager/) the best overall password manager in 2023.

At some point, I'd like to use it so I can see by myself real differences with **pass**.

Anyway, you can see a comparison [here](https://www.slant.co/versus/2840/19421/~pass_vs_bitwarden).

## Conclusion

I really like the simplicity of **pass**, being just a CLI utility that stores everything locally. That being said, I'm open to test other options with different approaches.

This post is the first one in a series about [password managers](https://blog.davidmontesdeoca.es/series/password-managers). In the next post I'll talk about the browser extension I use in combination with **pass**.

Thank you for reading and see you in the next one!
