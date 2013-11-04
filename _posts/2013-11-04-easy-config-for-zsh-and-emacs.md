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

I use zsh rather than bash because it is faster, it has improved tab completion, globbing, array handling and of course
is fully customisable. I use emacs, mostly because I am used to it, it is simple, fast, good languages and framework
support, easy key binding. It is really a great tool.

My main problem was sometimes, mostly for work, I needed to have a fully working instance of zsh and emacs, and I
struggled sometimes with several zsh and emacs install.

But recently, I found a way to do both very quickly :

## ZSH Config

Lets say you installed zsh. One way to quickly configure it is to open your *.zshrc*, using the following command :

`emacs ~/.zshrc`

But if you don't know, or don't remember, what to put in there, you will have to spend sometimes on google to get your
answer. Pretty annoying.

Fortunately there is this little **great** github project called [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)
(Not kidding).

### Description
[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) is a great thing to know. As the author describes, it is :

> ... an open source, community-driven framework for managing your ZSH configuration. It comes bundled with a ton of
> helpful functions, helpers, plugins, themes, and few things that make you shout

It is a complete .zshrc config. And one thing which is pretty cool is that it keeps your old .zshrc. Thus if you have
some decent alias, you won't loose them.

To install on your computer, you should firstly check if you have curl on your machine, then type the following command :

`curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh`

(you could also launch it with wget : `wget --no-check-certificate
https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh`)

To be continued ...
