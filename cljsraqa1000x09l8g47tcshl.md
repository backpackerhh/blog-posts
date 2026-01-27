---
title: "The one with OpenSSL issues installing older Ruby versions on Ubuntu 22.04"
seoDescription: "Learn how to fix OpenSSL issues when installing older Ruby versions on Ubuntu 22.04."
datePublished: Fri Jul 07 2023 15:53:33 GMT+0000 (Coordinated Universal Time)
cuid: cljsraqa1000x09l8g47tcshl
slug: the-one-with-openssl-issues-installing-older-ruby-versions-on-ubuntu-2204
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1751186368396/ca1f9113-f501-4288-98b3-980d7fbec3dd.png
tags: ubuntu, linux, ruby, openssl, ubuntu-2204

---

Recently I did a fresh install of Ubuntu on my laptop. As usual, I installed the latest [LTS](https://ubuntu.com/blog/what-is-an-ubuntu-lts-release) version available, 22.04 in this case.

As part of configuring the operating system, I always install a version manager for every programming language I'm working with.

If you wanna know what a version manager is, and how to configure and use it in Python, you will find an [article](https://davidmontesdeoca.dev/the-one-with-a-version-manager-in-python) on this blog about it.

When it comes to Ruby, although there are popular options such as [rbenv](https://github.com/rbenv/rbenv), my trusted version manager is still [RVM](https://rvm.io/) because it has always allowed me to manage different versions of the language and different gemsets in an easy and fast way. So no reason to change so far.

However, this time I ran into an error related to [OpenSSL](https://www.openssl.org/) when trying to install some older Ruby versions. But let's be clear that this is a [known problem with the language itself](https://bugs.ruby-lang.org/issues/18658).

## The Ruby issue

In the project where I'm currently working on, we use Ruby 2.7.8, which has already reached its [EOL](https://www.liquidweb.com/blog/eol-software/), as it was confirmed in the [release note of that version](https://www.ruby-lang.org/en/news/2023/03/30/ruby-2-7-8-released/) just a few months ago. The Ruby team recommends to start the migration to Ruby 3+ immediately, but, as you probably know, changing the version of a language or framework usually isn't a trivial task and it isn't up to me to decide when to do it anyway.

So I tried to install the Ruby version I needed:

```bash
$ rvm install 2.7.8
...
ruby-2.7.8 - #extracting ruby-2.7.8 to /usr/share/rvm/src/ruby-2.7.8 - please wait
ruby-2.7.8 - #configuring - please wait
ruby-2.7.8 - #post-configuration - please wait
ruby-2.7.8 - #compiling - please wait
ruby-2.7.8 - #installing - please wait
Error running '__rvm_make install',
please read /usr/share/rvm/log/1681642549_ruby-2.7.8/install.log
There has been an error while running make install. Halting the installation.
```

However, I found the following error message in the log file:

```
+__rvm_make:0> make install
...
/usr/share/rvm/src/ruby-2.7.8/lib/rubygems/core_ext/kernel_require.rb:83:in `require': cannot load such file -- openssl (LoadError)
  from /usr/share/rvm/src/ruby-2.7.8/lib/rubygems/core_ext/kernel_require.rb:83:in `require'
  from /usr/share/rvm/src/ruby-2.7.8/lib/rubygems/specification.rb:2430:in `to_ruby'
  from ./tool/rbinstall.rb:846:in `block (2 levels) in install_default_gem'
  from ./tool/rbinstall.rb:279:in `open_for_install'
  from ./tool/rbinstall.rb:845:in `block in install_default_gem'
  from ./tool/rbinstall.rb:835:in `each'
  from ./tool/rbinstall.rb:835:in `install_default_gem'
  from ./tool/rbinstall.rb:799:in `block in <main>'
  from ./tool/rbinstall.rb:950:in `block in <main>'
  from ./tool/rbinstall.rb:947:in `each'
  from ./tool/rbinstall.rb:947:in `<main>'
make: *** [uncommon.mk:380: do-install-nodoc] Error 1
+__rvm_make:0> return 2
```

In summary:

> cannot load such file -- openssl (LoadError)

I did some research on this error and [found](https://github.com/rvm/rvm/issues/5209) that the problem is related to the OpenSSL version available in Ubuntu.

Of course, I checked which version I had installed:

```bash
$ openssl version
OpenSSL 3.0.2 15 Mar 2022 (Library: OpenSSL 3.0.2 15 Mar 2022)
```

According to [this answer](https://stackoverflow.com/a/72216805/1477964) on StackOverflow, many libraries using OpenSSL (including Ruby) need to be adapted to use the changed APIs of OpenSSL. OpenSSL 3.0 is supported starting with version 3.0.0 of the [openssl](https://github.com/ruby/openssl) gem, which in turn was first shipped with Ruby 3.1.0.

As such, to be able to install a custom version of Ruby on Ubuntu 22.04 (with any version manager), you have to use at least Ruby 3.1.0, as older Ruby versions are unfortunately not supported directly. That answer is from a year ago, but apparently not much has changed since then.

It is suggested too to compile a custom OpenSSL 1.1.1 and compile Ruby against that custom OpenSSL if you need an older Ruby version on Ubuntu 22.04:

```
$ rvm pkg install openssl
$ rvm reinstall all --force
$ rvm install 2.7.8 --with-openssl-dir=/usr/share/rvm/usr
```

> Note: The second step is only necessary when any other Ruby version is already installed.

After that, I tried to install the Ruby version I needed again:

```bash
$ rvm install 2.7.8
Searching for binary rubies, this might take some time.
Found remote file https://rvm_io.global.ssl.fastly.net/binaries/ubuntu/22.04/x86_64/ruby-2.7.8.tar.bz2
Checking requirements for ubuntu.
Requirements installation successful.
ruby-2.7.8 - #configure
ruby-2.7.8 - #download
ruby-2.7.8 - #validate archive
ruby-2.7.8 - #extract
ruby-2.7.8 - #validate binary
ruby-2.7.8 - #setup
ruby-2.7.8 - #gemset created /usr/share/rvm/gems/ruby-2.7.8@global
ruby-2.7.8 - #importing gemset /usr/share/rvm/gemsets/global.gems..................................
ruby-2.7.8 - #generating global wrappers........
ruby-2.7.8 - #gemset created /usr/share/rvm/gems/ruby-2.7.8
ruby-2.7.8 - #importing gemsetfile /usr/share/rvm/gemsets/default.gems evaluated to empty gem list
ruby-2.7.8 - #generating default wrappers........
```

And I double checked that everything had gone as expected:

```bash
$ rvm list
=* ruby-2.7.8 [ x86_64 ]

# => - current
# =* - current && default
#  * - default
```

The next step was then to install project gems:

```bash
$ bundle install
```

That command started to install the gems until suddenly the following error messages started to appear:

```
Retrying fetcher due to error (1/4): Bundler::Fetcher::CertificateFailureError Could not verify the SSL certificate
for https://gems.contribsys.com/. There is a chance you are experiencing a man-in-the-middle attack, but most likely
your system doesn't have the CA certificates needed for verification. For information about OpenSSL certificates,
see https://railsapps.github.io/openssl-certificate-verify-failed.html. To connect without using SSL, edit your
Gemfile sources and change 'https' to 'http'.

Retrying fetcher due to error (2/4): Bundler::Fetcher::CertificateFailureError Could not verify the SSL certificate
for https://gems.contribsys.com/. There is a chance you are experiencing a man-in-the-middle attack, but most likely
your system doesn't have the CA certificates needed for verification. For information about OpenSSL certificates,
see https://railsapps.github.io/openssl-certificate-verify-failed.html. To connect without using SSL, edit your
Gemfile sources and change 'https' to 'http'.

Retrying fetcher due to error (3/4): Bundler::Fetcher::CertificateFailureError Could not verify the SSL certificate
for https://gems.contribsys.com/. There is a chance you are experiencing a man-in-the-middle attack, but most likely
your system doesn't have the CA certificates needed for verification. For information about OpenSSL certificates,
see https://railsapps.github.io/openssl-certificate-verify-failed.html. To connect without using SSL, edit your
Gemfile sources and change 'https' to 'http'.

Retrying fetcher due to error (4/4): Bundler::Fetcher::CertificateFailureError Could not verify the SSL certificate
for https://gems.contribsys.com/. There is a chance you are experiencing a man-in-the-middle attack, but most likely
your system doesn't have the CA certificates needed for verification. For information about OpenSSL certificates,
see https://railsapps.github.io/openssl-certificate-verify-failed.html. To connect without using SSL, edit your
Gemfile sources and change 'https' to 'http'.

Could not verify the SSL certificate for https://gems.contribsys.com/.
There is a chance you are experiencing a man-in-the-middle attack, but most likely your system doesn't have the
CA certificates needed for verification. For information about OpenSSL certificates, see
https://railsapps.github.io/openssl-certificate-verify-failed.html. To connect without using SSL, edit your Gemfile
sources and change 'https' to 'http'.
```

Then I visited the [link](https://railsapps.github.io/openssl-certificate-verify-failed.html) included in the error message for more information and found a possible solution for Ubuntu, although that solution was for the _openssl_ package on Ubuntu 12.04. Surprisingly, 11 years later the solution has not changed much.

As the error is happening in my development environment, I decided to search for the last available version of **libssl-dev** instead:

```bash
$ apt list -a libssl-dev
Listing... Done
libssl-dev/jammy-updates,jammy-security,now 3.0.2-0ubuntu1.10 amd64 [installed]
libssl-dev/jammy 3.0.2-0ubuntu1 amd64
libssl-dev/jammy 1.1.1l-1ubuntu1.4 amd64

libssl-dev/jammy-updates,jammy-security 3.0.2-0ubuntu1.10 i386
libssl-dev/jammy 3.0.2-0ubuntu1 i386
libssl-dev/jammy 1.1.1l-1ubuntu1.4 i386
```

And I installed the corresponding version:

```bash
$ sudo apt install libssl-dev=1.1.1l-1ubuntu1.4
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Suggested packages:
  libssl-doc
The following packages will be DOWNGRADED:
  libssl-dev
0 upgraded, 0 newly installed, 1 downgraded, 0 to remove and 7 not upgraded.
Need to get 1.732 kB of archives.
After this operation, 4.451 kB disk space will be freed.
Do you want to continue? [Y/n]
Get:1 https://ppa.launchpadcontent.net/rael-gc/rvm/ubuntu jammy/main amd64 libssl-dev amd64 1.1.1l-1ubuntu1.4 [1.732 kB]
Fetched 1.732 kB in 1s (2.092 kB/s)
dpkg: warning: downgrading libssl-dev:amd64 from 3.0.2-0ubuntu1.10 to 1.1.1l-1ubuntu1.4
(Reading database ... 352205 files and directories currently installed.)
Preparing to unpack .../libssl-dev_1.1.1l-1ubuntu1.4_amd64.deb ...
Unpacking libssl-dev:amd64 (1.1.1l-1ubuntu1.4) over (3.0.2-0ubuntu1.10) ...
Setting up libssl-dev:amd64 (1.1.1l-1ubuntu1.4) ...
```

That package is specifically geared towards developers who somehow need to use the OpenSSL library.

I ran the command to install the project gems again:

```bash
$ bundle install
```

And this time all the gems were successfully installed.

## Holding the package version

Later on, every time I applied the updates available in the operating system, I encountered the same error than before when I tried to do something related to Rails: start the server, start the console, run the tests....

As a reminder, the error is as follows:

> cannot load such file -- openssl (LoadError)

The solution was to downgrade the `libssl-dev` package version again. However, I didn't understand why that was happening to me again and again.

I did some research and found [this great article](https://itsfoss.com/prevent-package-update-ubuntu/) where the solution is explained in a very simple way.

To prevent the package from being updated, run following command:

```bash
$ sudo apt-mark hold libssl-dev
libssl-dev set on hold.
```

Note that the tab completion doesn't work with the `apt-mark` command. You need to type the exact name of the package.

To list all the packages that are held you can use:

```bash
$ apt-mark showhold
libssl-dev
```

Now if you apply all the updates, you will see that `libssl-dev` is no longer unintentionally updated:

```bash
$ sudo apt upgrade
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
libssl-dev
0 upgraded, 0 newly installed, 0 to remove and 1 not upgraded.
```

## Conclusion

Although it's recommended upgrading to Ruby 3+, if you for whatever reason cannot do it yet, you still can successfully manage and install older Ruby versions on Ubuntu 22.04, ensuring compatibility with the required OpenSSL version and avoiding unexpected package updates.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]