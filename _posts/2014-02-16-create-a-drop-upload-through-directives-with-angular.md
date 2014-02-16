---
layout: post
title: "Create a drop upload through directives with Angular"
description: ""
category: ""
tags: []
---
{% include JB/setup %}

## Introduction

I have been working with angular for sometimes now and it is quite a wonder. One thing I find very interesting is the possibility to manipulate the DOM that offer [directives](docs.angularjs.org/guide/directive).

In this article I am going to explain a bit what directives are through the creation of a _upload by drop widget_.

## How I did it before

When I was using tools like [JQuery](jquery.com), to do a drop using a plugin like [Dropzone](dropzonejs.com). I never did really try to make one myself (bad thing I know) for I never really had the time.

But on an application I had to remake, I had to integrate several drop widget, and I found it is quite easy to implement such a feature with Angular. Let me show you.

## What are directives

Basically directives are ... well they really are directives to Angular, written in the DOM, telling the framework how to act, when it does encounter a specific directive in the html.

Thus there is a great chance that you already use directives if you use Angular. Basically if you did use a `ng-show` or a `ng-repeat`, or any else with `ng`, you did use directives.

> NB : In brief a ng-show directive allows the element to observe the value of the given parameter (when you do ng-show='myParam') and change its display depending on the parameter value.

## Write Directive

To declare a directive, you simple add it to your module. So for an `example.module` module, you would write :

    angular.module('example.module', []).directive('myDirective', function () {
        ...
    }

> NB : Be careful on the name you choose. Camel case in JS will be '-' separated in the html. Hence 'myDirective' will become 'my-directive' in the DOM

### Options

They are several options for a directive, I am going to detail a few of them, you can check the [documentation](http://docs.angularjs.org/guide/directive) for more detail :

* _restrict_ :
    - The restrict option restricts the possible matches for your directive in the scope. A stands for __A__ttribute, and E for __E__lement, AE for both (some other can be used, please refer to the documentation)
* _scope_ :
    - One thing I should mention now on directive, is that they each have their own scope. But you can bind some things between the directive's scope and yours, I am going to detail it after.
* _link_ :
    - Basically it is a function which angular will call when your directive is injected in the DOM. This is where you want to bind your events for example
* _template_ : With this you declare you directive template, nothing fancy.

## Back to the widget

I am a big fan of learning by example, so lets dive into directive creation. First thing first, I want my directive to be declared as `<my-upload>` in the HTML, I won't bother with a template, each widget will have the same. Because I want to do an upload and I will redirect after upload I will need the `$source ` and `$location` modules. For the moment I don't really need anything else from the global scope. And finally I will only upload `tar.gz` files. So My directive will be declared as this :

    angular.module('example.module', []).directive('myUpload', function ($http, $location) {
        return {
            restrict: 'E',
            scope: {},
            link: function (scope, element, attributes) {
                ...
            },
            template: '<div><h2>Drop Files here</h2></div>'
        }
    });

As you can see, I restricted my directive to the element, I emptied my scope, gave it a template.

The interesting thing is of course the link function. It accepts three arguments. The `scope`, which is the directive scope, the `element`, which the [element](http://docs.angularjs.org/api/angular.element) of the directive, so you can manipulate it easily. And finally the attributes are the directive html attributes.

### Implement the link function

Well, your directive is almost over, now we took care of the _graphical_ part (no very complicated I reckon), lets take care of the functionnal one, and it'll be over.

To implement the drag and drop upload, I need to bind four events : `dragenter`, `dragleave`, `drop` and `dragover`. For anything but _drop_, you can simply do nothing, so, link function becomes :

    function (scope, element, attributes) {
        element.bind('dragenter', function(e) {
            e.stopPropagation();
            e.preventDefault();
        });
        element.bind('dragleave', function(e) {
            e.stopPropagation();
            e.preventDefault();
        });
        element.bind('drop', function (dropEvent) {
            ...
        });
        element.bind('dragover', function (e) {
            e.stopPropagation();
            e.preventDefault();
        });
    }

Of course, you should more likely add a class to your element, or anything else to warn the user something is happening.

Ok now last step, lets implement the _drop_ function :

    function (dropEvent) {
        dropEvent.stopPropagation();
        dropEvent.preventDefault();
        if (dropEvent.originalEvent.dataTransfer.files[0].type === 'application/x-gzip') {
            scope.$apply(function() {
                var payload = new FormData();
                payload.append('file', dropEvent.originalEvent.dataTransfer.files[0]);
                $http({
                    method:'POST',
                    url: YOUR/URL,
                    headers: { 'Content-Type': undefined },
                    data: payload,
                    transformRequest: angular.identity
                }).success(function(data) {
                    if (data.status == 100) {
                        $location.path('YOUR/NEW/URL');
                    }
                });
            });
        }
    });

So what I do, I stop the event, I check the file type, if it is a tarball, I use `$apply` on my scope (the directive's one) to launch the upload function, which will create a new [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData), which allows me to send the file as data in a `POST` request.

Two things are important : The header as a `Content-Type` of `undefined`, the browser will by itself change it to `multipart/form-data`, if we don't it will fail on the [boundary parameter](http://www.w3.org/Protocols/rfc1341/7_2_Multipart.html). Also the `transformRequest` which returns the payload as it is, so Angular won't serialize it (which it basically does as its `transformRequest` function).

And thats it, you can in your backend handle the uploaded file and then send back a `json` response with a `status` set to 100 if anything went fine.

Of course this is a basic example, and you more likely will need to make changes, but I believe this example demonstrate how easily you can create reusable DOM elements. Now I simply declare `< my-upload />` in my html, and I'll have this upload widget.

## A brief paragaph on scope

As I mentionned before, the directive has its own scope. It means that you do not have direct access to your _global_ scope variables. To do so, you can define a binding between those scopes, using the `scope` option :

    ...,
    scope: {
        'twoway': '=',
        'dirtoglo': '@',
        'glotodir': '&'
    },

Basically, `=` means two way binding, `&` exposes a variable from the global scope to the directive scope and `@` does the contrary.

So in a directive you declare your variable like this :

    <my-directive twoway='...' dirtoglo='...' glotodir='...' />

And you have direct access to them in the `scope` object. Be aware that binding as a direction so be careful when you want to update your variables.

Finally do not forget you have direct access to htm attributes in the link function.

## Conclusion

That's it ! I hope you had a nice reading. Even if this example is quite brief, it clearly shows the possibility directives offer.

I recommand you to check the [ng-conf](https://www.youtube.com/user/ngconfvideos/videos) videos if you need a deeper look into Angular. If you are new, I can only recommand you [Dan Wahlin](https://www.youtube.com/watch?v=i9MHigUZKEM) excellent video on almost everything you need to know to start developping on Angular.