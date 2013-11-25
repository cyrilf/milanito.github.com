---
layout: post
title: "Security in sails"
description: ""
category: "How to implement security in sails"
tags: [sails javascript nodejs]
---
{% include JB/setup %}

## Introduction

On this blog, I already made a short article on
[Sails](http://sailsjs.org/). It a very easy to use web framework. In
this new article, we will try to focus on how the security is
implemented, or at least could be.

## Definition

Sails is built using [Express](http://expressjs.com/), meaning that it
works just like it. It provides cookie based section, accessible
through `req.session`.

Throughout the rest of the article, we will imagine a small sails
application, with two parts : `/front/`, which will be accessible by
any logged user, and an `/admin/` that will be only accessible by
admin users.

All the actions for the front will be in the `FrontController` and the
admin ones will be in the `AdminController`.

## Basic authentification

Lets define the basic authentication for our site. A basic
implementation would be in each controller, for each action :

    action: function(req, res) {
        if (req.session.isAuthenticated) {
            ... Performs our actions
        } else {
            ... Redirect to login maybe ?
        }
    }

But it is pretty boring to put this in all our controllers, even more
if it only redirects to the login page. This is where the policies are
great !

### Policies

Policies in sails, just like in Express, are tools that help defining
authorization and access control. They are launch before the
controller and can be chained.

A policy allows you to have access to the `req` and `res` objects. I
bet you see it now, lets use a policy that checks if we are online.

You define a policy by creating a new file in the `api\policies`
directory. For our example lets create a `isAuthenticated.js` file,
which would be like this :

    module.exports = function(req, res, next) {

    // User is authenticated, proceed to the next policy,
    // or if this is the last policy, the controller
        if (req.session.isAuthenticated) {
            return next();
        }

    // User is not allowed
    // (default res.forbidden() behavior can be overridden in `config/403.js`)
    // return res.forbidden('You are not permitted to perform this action.');
        return res.redirect('/login');
    };

### Apply the policy

Now we have our policy, we need to use it on a specific controller,
for a specific action. To do so, we need to modify the file defining
the behavior of the policies, the `config/policies.js`. The
configuration is pretty straighforward, you define for each controller
and each action the policy it has to following, so in our example :

    // Default policy for all controllers and actions
    // (`true` allows public access `false` forbids it)
    '*': true,
    FrontController: {
        // Default for front
        '*': false,
        // To see the action1, we need to be authenticated
        action1: "isAuthenticated",
        ...
    },
    AdminController: {
        '*': false,
        ...
    },

## Complex Authentication

By wait, we said before we need to be authenticated *and* admin to be
able to access admin. Should we then create a specific policy for the
admin part ?

*No we don't*. Like I already said, policies can be chained. So lets
 imagine we have a second policy for the admin :

    module.exports = function(req, res, next) {
        if (req.session.isAdmin) {
            return next();
        }

        // We redirect to the front
        return res.redirect('/front');
    };

What we need now in the `config/policies.js` is to write :

    AdminController: {
        '*': false,
        action1: ["isAuthenticated", "isAdmin"]
    },

Now the action1 for the administration requires the user to be
authenticated *and* admin.

## Conclusion

With this simple tools, we can easily implement a cookie-base
authentication, but also a role base access control. But we can also
do pretty stuff like : limit the file uploaded size, limit the
authorized file extensions, etc ...

