---
layout: post
title: "Jade is magic"
description: "Jade is a true wonder, let me show you why"
category: "web"
tags: [sails jade]
---
{% include JB/setup %}

## Introduction

For some times now I use [sails](http://sailsjs.org) at work and for myself. I was using ejs as a view engine, but I was not completely confortable with it. Mostly it was like any other template engine, lots of html, and on this one, variable have an ugly syntax.

Anyway, I recently discovered [Jade](http://jade-lang.com/), and I am going to show you why it is clearly a wonder.

## Installation

To install jade on your project, you need to update your `package.json` file by adding the following :

    dependencies {
        ...
        "jade": "0.35.0",
        ...
    }

Of course, you can use the version you want. `0.3.5` does everything I need at the moment.

Next you need to update your `view.js` file so you can use jade by changing the following value :

    engine: 'jade',
    layout: false

The engine value is pretty self explanatory, as for the layout, we simply tell the engine that we won't be using a layout file ... In fact we will, but that's for the second part.

You can now get rid of your ejs file !!!

## Using it

Let's try to compare basically ejs and jade.

### Rendering

The rendering in `sails` is completely the same, you use the same function the same way :

    // To render a view
    res.render("view_path", {data}, function(err, html) {
        // Do your stuff with the html
        });
    // To send a view
    res.view("view_path", {data})

*But we have just seen that we don't use a layout file*, does this mean that we will have to define a header and a script template for grunt, and we will have to include in all files ?

The short answer is ... **No**

To do the same mecanism as a layout view in jade, you simply create a layout file, you put the following tags for grunt :

    // STYLES
    // STYLES END
    ....
    // SCRIPTS
    // SCRIPTS END

And at the beginning of each of your files, you simply add the following line :

    extends path_to_your_layout

The path is relative, from where your view file is located.

## Syntax

Thats funny, I was just telling about how to extend a layout file and integrate grunt tags, but I did not even show you the best thing about jade, its syntax.

Jade use `div` as default or the html tag you provide, meaning :

    .div
    will result in :
    <div class="div"></div>

And 

    a.link
    will result in
    <a class="link"></a>

The class and id work the same and are chainable :

    #mydiv.classone.classtwo.onemoreclass
    will result in
    <div id="mydiv" class="classone classtwo onemoreclass"></div>

If you need to set a parameter, just put it in parenthesis, like :

    a#mylink.linkclass.coolclass(href="/my/place", data="something")
    will result in
    <a id="mylink" class="linkclass coolclass" href="/my/place" data="something"></a>

As for the content, you have two ways to put it. First one is inline :

    h1 One big title
    will result in
    <h1>One big Title</h1>

Or you can use indentation, everything indented one tab or two space is in the container :

    #mydiv.mydiv
        span#myspan in here
    will result in
    <div id="mydiv" class="mydiv"><span id="myspan">in here</span></div>

So lets say we integrated [Bootstrap](http://getbootstrap.com) in our site, a login form would only take something like :

    .container
        .row
            .col-md-6.col-md-offset-3.text-center
                form.form-signin.login-form(role="form")
                    h2.form-signin-heading Login Form
                    .form-group.username
                        input.form-control.username(type="text", placeholder="User name", required autofocus, name="username")
                    .form-group.password
                        input.form-control.password(type="password", placeholder="Password", name="password")
                    button.btn.btn-lg.btn-primary.btn-block.login-button(type="submit") Sign In

Pretty decent right ?

## Layout and block

Lets go back to the beginning, we talked about extending the layout file. But what if we want a header and footer always similar and simply have a `page` part that can change ? We simply use blocks :

In your layout file, if you do :

    .header
        ...
    body
        block body
        footer

Now you can do in your layouts file :

    extend path_to_layout
    block body
        #my-div
            h1 Welcome to the body of my page

Of course, because you have extention, you have inclusion, so to include a partial layout file, simply do :

    include path_to_layout

The path still being relative to the view file folder

## Variables

Ok, now we know how to build a html file, but in view rendering, one important thing is the possibility to pass variables to my view. To so in jade, you pass the data as usual in the controller :

    res.render("view_path", {data: somedata}, function(err, html))

And in jade you use the `=` symbol to tell the engine to try to interpret your value, meaning :

    h1= data
    will result in
    <h1>somedata</h1>

But also :

    a(href=data)
    will result in
    <a href="somedata"></a>

You can also tell jade not to interpret the line with the `!=` symbol.

### Mixin

One **great** thing with Jade, is the ability to define mixin for complicated parts of the view.

Lets say I want to add a nice line in a `list-group`, using bootstrap. I define my mixin in the view file, personnaly I define them at the beginning of the file. For example :

    mixin object(name, id)
        link = "/object/" + id
        .row
            .col-md-1.pull-right
                span.glyphicon.glyphicon-remove.cursor.remove-object(object=id)
            .col-md-4
                a.btn.btn-default.btn-lg(href=link) #{name}

This mixin first define a `link` variable, which is cool because I had not speak about it, so now you now how to do that, and simply *returns* the part I want, which is a button which leads to the `link` page and a remove icon. It requires two variables, a name for the button and and id for the `object` parameter of the remove icon.

Now for my list, I can do :

    .row
        .col-md-12
        h3 My objects
        ul.list-group
            - each campaign in campaigns
            li.list-group-item
                +campaign(campaign.name, campaign.id)

Now my list is kinda pretty, as my code is. As you can see I use a loop, which has a really easing syntax :

    - each val, key in objects
    ...

You can obviously omit the key parameter, as I just did in the example. The `-` token starts unbuffered code, meaning it would not be printed but intepreted.

You can also use a more classic syntax :

    - for (var x = 0; x < 3; x++)

This is a really usefull feature, because it allows you to maintain you view as you would in any source file. You define a *render way* with your mixin and if you need to make modification of this rendering, you modify your mixins, which is at the beginning of your file. This allows you to maintain a very powerful front end, in the eyes of the user.

### Conditionnal

What would really be the use of function if one could not test the value of its variable ? Nothing really, for each task we would need to write a new function ...

Of course Jade is not this boring and include conditionnal testing. Its syntax is no different than any you use. Let image you passed a `{data: somedata}` object to you view, to use a `if`, just do :

    if data
        p I have some data
    else
        p no data man

Jade also allow inline testing, such as :

    body(class=authenticated?'authed':'anon')
    which would result in 
    <body class="authend"></body>
    if authenticated exists and is not false
    <body class="aunon"></body>
    otherwise

Finally Jade also case testing, with the following syntax :

    case number
        when 0
            p you have nothing
        when 1
            p you have an object
        default
            p you have #{number} objects

## Conclusion

I hope you find what you need in Jade. For me it is a real wonder. It has a really nice syntax and is easy to handle.

I tend to divide my views this way :

* I use a main layout file in which I define my header and include my scripts
    - I can sometimes use partials to define this file
    - I define several blocks for what I need
* In each file I extend my layout and fill the blocks
    - I use the common mixins to organize the elements
    - I use private mixins to organize the style of my elements

This way I can rely on a css preprocesser like [less](http://lesscss.org) and when my front environnement is validated, most of the time I just need to modify my mixins.

Thus the main advantage of jade is that through a relatively great syntax and ease of utilisation, it tends to make you write more consistent code, hence increasing the quality of your apps.

Hope you had a nice reading.