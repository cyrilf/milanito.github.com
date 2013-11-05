---
layout: post
title: "Easy Config for ZSH and Emacs"
description: "Have some problem configuring emacs and zsh?"
category: "config"
tags: [config emacs zsh]
---

{% include JB/setup %}

`I am an emacs and zsh user.`

Hello all,

I use zsh rather than bash because it is faster, it has improved tab
completion, globbing, array handling and of course is fully
customisable. I use emacs, mostly because I am used to it, it is
simple, fast, good languages and framework support, easy key binding.
It is really a great tool.

My main problem was sometimes, mostly for work, I needed to have a
fully working instance of zsh and emacs, and I struggled sometimes
with several zsh and emacs install.

But recently, I found a way to do both very quickly :

## ZSH Config

Lets say you installed zsh. One way to quickly configure it is to open
your *.zshrc*, using the following command :

`emacs ~/.zshrc`

But if you don't know, or don't remember, what to put in there, you
will have to spend sometimes on google to get your answer. Pretty
annoying.

Fortunately there is this little **great** github project called
[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) (Not kidding).

### Description

[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)
is a great thing to know. As the author describes, it is :

> ... an open source, community-driven framework for managing your ZSH
> configuration. It comes bundled with a ton of helpful functions,
> helpers, plugins, themes, and few things that make you shout

It is a complete .zshrc config. And one thing which is pretty cool is
that it keeps your old .zshrc. Thus if you have some decent alias, you
won't loose them.

### Installation

To install on your computer, you should firstly check if you have curl
on your machine, then type the following command :

`curl -L
https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh
| sh`

(you could also launch it with wget : `wget --no-check-certificate
https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh
-O - | sh`)

What it does is it simply install a bunch of insane feature to your
zsh. You can customize your
[theme](https://github.com/robbyrussell/oh-my-zsh/wiki/themes) and
install great plugins (I personally use a lot of them, such as ant,
git, etc ...)

And finally, you can also develop your own plugin and putting a
_plugin.zsh_ in the _custom/plugin_ directory. (I will probably make
a post about it later)

## Emacs config

### Introduction

Lets imagine your are new to emacs. When I asked you to do `emacs .zshrc`
a program not really pretty appeared. This is the thing with emacs. It
can be the best thing if your customize it, but for the eyes that have
been used to _pretty editor_, it is ugly.

Making your own configuration is pretty straightforward. It uses
[lisp](http://common-lisp.net/) language. It is pretty flexible but you
will have to find a way to share all your configs, maybe rewrite it a
bunch of time.

Recently I change my computer at work and at home. Even if I made some
backups, I was pretty annoyed by the fact I had to check everything
twice. This is when I found this project :
[.emacs.d](https://github.com/magnars/.emacs.d) (the name of the directory
you normally put your emacs configuration).

### Installation

If you have git installed on your computer (you really should if you don't),
simply enter the following command in your home directory:

> `git clone --recursive git://github.com/magnars/.emacs.d.git`

After that if you launch emacs you will see it install the missing
packages. But be aware that it is not over yet.

### Problem resolution

When you first launched emacs, you could see that mostly one package
is missing: _nrepl_. First thing I tried is to add
[marmalade](http://marmalade-repo.org/) repo :

> First open the setup package file :
> `emacs ~/.emacs.d/setup-package.el`
> Under line 5, copy the following line :
> `(add-to-list 'package-archives '("marmalade" . "http://marmalade-repo.org/packages/"))`

Unfortunately, even if the
[nrepl](http://marmalade-repo.org/packages/nrepl) is available on marmalade
emacs would not find it.

No worries. After all nrepl is only a elisp client to connect to nrepl
clojure servers. Personnaly I don't use closure. So what I did was :

> I opened the init file :
> `emacs ~/.emacs.d/init.el`
> Under the comment _Install extensions if they're missing_, in the package list,
> I removed the nrepl package

### Customization

After emacs is correctly running, I recommand installing a few
packages. I will probably make an article about that. But I urge you
to try _smex_. It is simply an enhanced `M-x`, but it is so great !!!

Thank you for reading, I hope this could help you :)
