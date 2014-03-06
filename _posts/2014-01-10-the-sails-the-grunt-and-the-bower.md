---
layout: post
title: "The Sails, the Grunt and the Bower"
description: "For three tools, managing libraries wasn't hell, it was practice"
category: "sails javascript"
tags: [sails, javascript, bower, grunt]
---
{% include JB/setup %}

## Introduction

Today I am going to talk about managing front and back end packages in a sails application. It is something I find quite important working in projects with several members, especially in a such swiftly moving world as the node's one is.

## Back End

As usual you create your project with the `sails` command. Of course it is always good to use linkers but it is up to you.

    sails new yourproject --linker

Managing back end packages is pretty straighfoward, use the `package.json` file. There is really no purpose to this part :)

## Front End

### Bower and Problem

Lets now concentrate on something new. Some times ago I discovered a fantastic tool, [bower](http://bower.io/), which is a front end package manager whose goal is to help developers' life, clearly.

To install bower, you simply enter the following command

    npm install -g bower

And it is avaible with the `bower` command. I am not going to enumerate the documentation, but it is a classic package manager with classic functions. The only problem was the way it behave :

* I can use the `bower` command to download a front library precisely where I want it to be (Mostly in the `assets/linker` folder). The little problem with this solution is that it is quite boring to navigate to each directory and download the package. This without adding the fact that bower, in order to work anytime downloads **EVERYTHING** from a package (go and see `jquery.ui` package for yourself)
* I can use a `bower.json` configuration file, but it will download everything in a `bower_components` folder. Of course you can change this folder by editing a `.bowerrc` file ([Specifications](https://docs.google.com/document/d/1APq7oA9tNao1UYWyOm8dKqlRP2blVkROYLZ2fLIjtWc/edit#)). But I does not correct the *everything* problem. This would cost me to rewrite too much of my grunt configuration.

### Node and Power

Fortunately there is this wonderful package : [grunt-bower-task](https://github.com/yatskevich/grunt-bower-task), which allows you to create a grunt task for bower and to extend its configuration.

First you need to add it as a dependency to you project, so in your `package.json` :

    {
        "name": "WhatAnApp,
        ...
        "dependencies": {
            "sails": "0.9.8",
            ...
            "grunt-bower-task": "0.3.4",
        },
        ...
    }

The 0.3.4 version was the one used during the redaction of this article, as for the 0.9.8 sails version. After a `npm install`, you need to update your project's `Gruntfile.js` :

    ...
    grunt.loadTasks(depsPath + '/grunt-contrib-coffee/tasks');
    //First add the module to grunt :
    grunt.loadNpmTasks('grunt-bower-task');
    //Then add the task
    grunt.initConfig({
      pkg: grunt.file.readJSON('package.json'),
      // Bower Task
      bower: {
        install: {
          options: {
            targetDir: './assets/linker',
            layout: 'byType',
            install: true,
            verbose: false,
            cleanTargetDir: false,
            cleanBowerDir: true,
            bowerOptions: {}
          }
        }
      },
      ...

What does it do ? It tells  grunt : "Ok, add this module, he'll know what to do." and then in the task definition "Ok, with this module, you install in the `assets/linker` folder, you divide them by type, don't tell how it went, but clean the bower stuffs after, not touch the assets linker folder (because there might be other things in there right?)".

To check if everything went fine, simply enter the following command :

    grunt --help

You should see the `bower` task listed. This of course if you have [grunt](http://gruntjs.com/) installed.

Now we need to configure bower to handle the package the way we want. Lets take an example of a `bower.json` which would download [Jquery](http://jquery.com/) :

    {
      "name": "MY Bower",
      "version": "0.0.1",
      "dependencies": {
        "jquery": "1.10.2",
      }
    }

If you now run the `grunt bower` command, you should see a `jquery` folder in your `assets/linker` folder. That is not quite what we wanted. What you need to do is to extend your bower configuration by adding the following :

    ...
        },
        "exportsOverride": {
          "jquery": {
            "js/vendor": "**/jquery.min.js"
          }
        }
    }

This would get the `jquery.min.js` and put it in the `assets/linker/js/vendor/jquery` folder, right where it should be.

But what if I want something a bit more complicated ? Lets integrate [bootstrap](http://getboostrap.com). So I update my dependencies : 

    "dependencies": {
      "jquery": "1.10.2",
      "bootstrap": "3.0.3" 
    },

And my `exportsOverride` :

    "exportsOverride": {
      "jquery": {
        "js/vendor": "**/jquery.min.js"
      },
      "bootstrap": {
        "js/vendor": "dist/js/bootstrap.min.js",
        "styles/vendor": "dist/css/*.min.css",
        "fonts/vendor": "dist/fonts/*.*"
      }
    }

Lets see : It will put the `bootstrap.min.js` file in `assets/linker/js/vendor/bootstrap` folder, all the minified css files in the `assets/linker/style/vendor/bootstrap` and I let you guess for the fonts :)

That is really **three lines** of configuration to integrate bootstrap in a web application ! No more than it should be I guess.

So now when I run `grunt bower` I have a front end which as been provided with `jquery` and `bootstrap`. So now I can simply add those lines to my `.gitignore` file :

    ########################
    # Assets Folder
    ########################
    assets/linker/js/vendor
    assets/linker/styles/vendor
    assets/linker/fonts/vendor
    assets/images/vendor

I supposed I do not have to say that the paths in `exportsOverride` is relative, so to put something in the `assets/images/vendor` folder, use `"../images/vendor"`

From now I will not have to commit my front libraries in my repositories. To initiate a project I just cloned, I would simply have to do `npm install && grunt bower`, front and back end will be handled !

And also package version management and dependencies are handled through `bower`. You can greatly improve you projects this way, for it is way clearer and cleaner.

### Precision

This is no magic. You will still have to modify your `Gruntfile.js` if you need specific injection. You might need to tweek some things, for many packages differ from each others.

You might sometimes need to do a `grunt bower` on a library without any configuration just to download the whole thing, some you'd know how to divide it.

## Conclusion

But This is really a nice thing. Not only your repositories might get lighter, I will rationalize your front end workflow. Fortunetaly no more "I added this plugin I found. The guy has updated it for two years but it seems ok" ...

I hope you add a nice reading.


