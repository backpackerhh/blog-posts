---
title: "The one with a version manager in Python"
seoDescription: "Learn how to manage multiple versions of Python with pyenv."
datePublished: Mon Apr 24 2023 07:48:15 GMT+0000 (Coordinated Universal Time)
cuid: clgujdkxx000009le40z05oh4
slug: the-one-with-a-version-manager-in-python
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/vb-3qEe3rg8/upload/ac768c8b690a167f782c218300ffb697.jpeg
tags: linux, aws, python, aws-lambda, pyenv

---

Python is a programming language that I have worked with several times throughout my career, but, believe it or not, I have never needed to use a version manager that would allow me to handle multiple versions of that language at the same time.

However, I have been using version managers in other languages that I work with more regularly for years, such as [RVM](https://rvm.io/) for Ruby or [NVM](https://github.com/nvm-sh/nvm) for JavaScript.

On my local machine with Ubuntu 22.04 I have Python 3.10.6 installed, but I needed Python 3.9 to run locally with [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) a Lambda function I've been working on lately, as that was the latest version supported by AWS.

> Note: Last week [AWS Lambda added support for Python 3.10](https://aws.amazon.com/about-aws/whats-new/2023/04/aws-lambda-python-3-10/).

Doing a quick research, I found several version managers for Python, such as [pythonz](https://github.com/saghul/pythonz) and [pyflow](https://github.com/David-OConnor/pyflow), but many people considered that the best option was to use [pyenv](https://github.com/pyenv/pyenv). So that's what I did.

I highly recommend that you read the [README of the project](https://github.com/pyenv/pyenv) as it is very well documented. Anyway, let me explain to you the steps I followed to set up **pyenv** properly.

## Installation

You will need the automatic installer for Linux distributions:

```bash
$ curl https://pyenv.run | bash
```

For other operating systems refer to the [installation section](https://github.com/pyenv/pyenv#installation) of the project.

## Shell

Run the following commands in your terminal for [ZSH](https://github.com/zsh-users/zsh):

```bash
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
$ echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
$ echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

For other shells refer to the [shells set up section](https://github.com/pyenv/pyenv#set-up-your-shell-environment-for-pyenv) of the project.

For changes to take effect, run the following command:

```bash
$ exec "$SHELL"
```

## Usage

### List all available versions in your system

```bash
$ pyenv versions
* system (set by /home/david/.pyenv/version)
```

As you can see, it shows an asterisk next to the currently active version.

### Display the currently active version

```bash
$ pyenv version
system (set by /home/david/.pyenv/version)
```

### List the all available versions

```bash
$ pyenv install --list | less
Available versions:
  2.1.3
  2.2.3
  2.3.7
  ...
```

### Install a new version

Maybe you will need to install Python [build dependencies](https://github.com/pyenv/pyenv/wiki#suggested-build-environment) before attempting to install a new Python version.

```bash
$ pyenv install 3.9.0
Downloading Python-3.9.0.tar.xz...
-> https://www.python.org/ftp/python/3.9.0/Python-3.9.0.tar.xz
Installing Python-3.9.0...
patching file Misc/NEWS.d/next/Build/2021-10-11-16-27-38.bpo-45405.iSfdW5.rst
patching file configure
patching file configure.ac
Installed Python-3.9.0 to /home/david/.pyenv/versions/3.9.0
```

Check again the list of all available Python versions:

```bash
$ pyenv versions
* system (set by /home/david/.pyenv/version)
  3.9.0
```

### Set a local application-specific version

```bash
$ pyenv local 3.9.0
```

That command writes the version name to a `.python-version` file in the current directory.

Check again the list of all available Python versions:

```bash
$ pyenv versions
  system
* 3.9.0 (set by /home/david/projects/recommender_lambda/src/.python-version)
```

And check again the currently active Python version:

```bash
$ pyenv version
3.9.0 (set by /home/david/projects/recommender_lambda/src/.python-version)
```

### Uninstall a version

```bash
$ pyenv uninstall 3.9.0
pyenv: remove /home/david/.pyenv/versions/3.9.0? [y|N] y
pyenv: 3.9.0 uninstalled
```

For more commands refer to the [command reference](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md) page.

## Conclusion

**pyenv** is quite easy to use. It is not surprising that it reminds me of other version managers I have used before, since it was forked from [rbenv](https://github.com/rbenv/rbenv) and [ruby-build](https://github.com/rbenv/ruby-build), and modified for Python.

I hope this story has been helpful to you, especially if you haven't used a version manager before, so you can avoid version conflicts while working on multiple projects that require different versions of a given programming language.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]