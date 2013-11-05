---
layout: post
title: "Ajax call in symfony"
description: "Lets see how we can easily make ajax calls using symfony Framework"
category: "symfony"
tags: [symfony ajax]
---
{% include JB/setup %}

## Introduction

Symonfy2 is such a great framework. I not a big fan of the php
language, but symfony is so powerful, I don't mind using it.

This article is not about how you can use symfony. So if you don't
know anything about it, I suggest you stop your reading and go
to their [site](http://symfony.com/) and try some tutorials.

Personnaly I use for most of my project always the same tools :
* [Symfony2](http://symfony.com/) as a backend framework
* [Jquery](http://jquery.com/) for the front end
* [Foundation](http://foundation.zurb.com/) for the HTML

If you like those stuff and need to quickly start a similar symfony
project, you could use the tool I made :
a [Symfony Python Installer](https://github.com/milanito/pythonSymfonyInstaller)

* To get it :

> `git clone https://github.com/milanito/pythonSymfonyInstaller.git`

* Then to launch it :

> `python directory_where_you_cloned_it/pythonSymfonyInstaller/symfony_install.py`

This tool will download all required _js_ and _css_ needed file and
put them in the `web/js` and `web/css` directories. Then you have to :

Put this in the header of your twig pages :

    \{\% javascripts 'js/lib/custom.modernizr.js' \%\}
    <script src="\{\{ asset_url \}\}" type="text/javascript"></script>
    \{\% endjavascripts \%\}

For modernizr (foundation), and just before closing the body :

    \{\% javascripts 'js/lib/jquery.min.js'
    'js/lib/foundation.min.js' \%\}
    <script src="\{\{ asset_url \}\}" type="text/javascript"></script>
    \{\% endjavascripts \%\}
    <script>
    $(document).foundation();
    </script>

There you go, your project is ready.

## Ajax calls

Lets say you are on your page, and you need to make an ajax call. Here is
the easy way to do so (I will consider you are at the root directory of
your project) :

### Generate controller

For this example, we will create a generic controller for ajax calls but
you can also put them as an action in your existing controller. To add a
controller, you can do it by hand or generate it.

To generate a controller, just type the following command whe you are
in the project directory :

> `php app/console generate:controller`

When creating the controller, do not forget to add an action. For this
article, we will create an action called _ajaxExampleAction_. Please
erase the annotations if you used the generation above the function.

Normally you should have the following in your controller file :

    public function AjaxExampleAction() {
    }

Next thing to do is to be able to call the controller :

* We have to open the _routing.yml_ situated the `app/config/` directory
* create a route for the ajax call using the following syntax

    route_for_ajax:
    pattern:   /ajax/example
    defaults:  \{ \_controller: YourBundle:YourController:ajaxEmplace \}

Ok, so now we can call our ajax, it will be avaible through the url
`server/application/web/ajax/example`

### Make it return a JSON file

Lets improve a bit our example and make the function return a very simple
JSON file. Just modify the function and at the following :

    $response = array("code" => 100, "success" => true, "message" => "here is our JSON");
    return new Response(json_encode($response));

Don't forget to add the `use Symfony\Component\HttpFoundation\Response;`
so you can use the Symfony responses.

This simply return a JSON. The framework will know what to do. If you
navigate to the url, you could see the JSON that will be returned.

    {"code" : 100, "success" : true, "message" : "here is our JSON"}

Ok now lets use this ajax call.

### Use the ajax call

In the next part I show you the way I use Ajax in symfony. I am not
saying this is the best way to do so, but I find it very easy and
confortable, which is what matters.

Lets say we have a `div` in our twig template, which if we click
on it will launch the ajax call. In our template we will have :

    <div id="myButton">Click me please</div>

What we need to do, in the javascript, is to call the ajax. So we
write the following function :

    function callAjax() {
        $('#myButton').click(function() {
            $.get(...)
        });
    }

The thing is : _We need the url for the ajax call!!_. To do so I tend
to add the url as a HTML param of my div, simply by doing so :

    <div id="myButton" ajax-call="\{\{path('route\_for\_ajax')\}\}">Click me please</div>

So now, the Javascript will look like this :

    function callAjax() {
        $('#myButton').click(function() {
            $.get($(this).attr("ajax-call"), function (data) { alert(data.message)});
        });
    }

This code will simply open an alert dialog with _"here is our JSON"_

### Additional Informations

This is a pretty straight way to make ajax call. You use the power of twig and
symfony route mapping to generate your ajax url.

If you need to make post calls, you can simply use the `$.post` jquery function
and give it the parameters you need.

## Conclusion

Here you go, you have no excuse not to use ajax in a sympfony project.
