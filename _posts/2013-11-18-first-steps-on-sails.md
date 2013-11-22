---
layout: post
title: "First Steps on Sails"
description: "Lets have a first look on Sails"
category: "sails javascript"
tags: [nodejs sails]
---
{% include JB/setup %}

## Introduction

Recently, I discovered a new Web Framework which runs on
[nodeJS](http://nodejs.org/) : [Sails](http://sailsjs.org/). For a few
time now, I tend to abandon heavy web language such as Java or PHP and
I am getting way closer look on NodeJS.

I really like Javascript, it is very confortable to work on, a full
object language, and because I have seen it with our trainee, it is
quite easy to learn.

When you develop a web app using sails, you use the same langage all
along your developement. This allows a data manipulation way easier and
most of the time a faster coding.

The catch with this kind of framework is that it is so straighfoward
that you might be tented to do stuff a bit messy. You have to be very
careful about your specifications before jumping into coding. Anyway
it is so nice to use.


## Installation

Installing sails is pretty straightforward. First you need to install
nodejs, by typping the following command (I am on Fedora Linux) :

    sudo yum install npm

Ok, now we need to add the `sails` module. To do so, we use the `npm`
command. NPM stands for *Node Package Modules*, it is simply a package
manager which allows us to add new packages to our node install. So to
install the sails package, enter to following command :

    sudo npm -g install sails

After the installation is complete, that's it, sails is ready to be
played with.

## Project Handling

In this example we will create a new project and show the main
features of sails.

### Creation

To create a new project called `testProject`, in the directory where
you want to create your project, type the following command :

    sails new testProject --linker

The `--linker` options allows us to inject our assets using grunt. Now
we can launch our project by goind into the newly created project
directory (`testProject`), and launch the command :

    sails lift

You can now navigate to the adress http://localhost:1337 to see your
website in action.

### Actions

To implements navigation on our web app, we need to define controllers
that will handle the requests. Lets create an example controller, with
an index action with the following command :

    sails generate example index

This will generate two files :

* The controller, in the `api/controllers` directory, named `ExampleController.js`
* The model, in the `api/models` directory, named `Example.js`

> **Note on route** : If you do not define your route in the
> `config/routes/js` folder, routes will be using this format :
> *controller_name*/*action_name* all in lowercase

Lets now open the controller file, the index function is simply :

    index: function(req, res) {
        ...
    },

you can see that the action is a function which needs two parameters :

* `req` : This is the request parameter, which allows us to access
  POST and GET parameters, session, etc ...
* `res` : This is the response parameter, which allow us to render
  view, return JSON, etc ...

For the example, lets make it return a html view, by modifying the
function this way :

    index: function(req, res) {
        return res.view("example/index", {});
    },

> **Note on view** : The view are situated in the `views` folder on
> the project root directory. You should put the view linked to your
> controller in the corresponding directory. You can also put partials
> view in subfolder.

In our case, the view will be the `index.ejs` file situated in the
`view/example` folder. EJS files are ajax templates. They autorise you
to do most of the work, meaning variable handling and loops. You can
change the views' layout by modifying the `config/views.js` file. The
basic layout include two custom block : `title` and `body`, meaning the
view will be the body part.

For this example simply put :

    <p>Hello Worl</p>

And that's it, you have your hello world, after lauching `sails lift`
of course.

## Conclusion

I wanted to make a clear short first article on sails for I will try
to do several others that will focus on specific parts of the
framework. Anyway since I discovered it I can stop using it. It is
very convenient to use for it has everything you need to build strong
web app, and it is very easy to use. Sure it does lack some features,
especially in the model handling, but it is so pratical to never change
langage all along a web app, that it is easily forgive.
