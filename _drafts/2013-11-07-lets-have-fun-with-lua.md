---
layout: post
title: "Lets have fun with Lua"
description: ""
category: "android"
tags: [android lua]
---
{% include JB/setup %}

## Introduction

I recently add to work with [Lua](http://www.lua.org/), and I reckon
this language is very nice. Basically, [Lua](http://www.lua.org/), is
a embeddable scripting language. It is used in many applications like
Adobe Lightroom or World of Warcraft. In fact it is currently the
leading scriptin software in games. Pretty impressive right ?

One big advantage of [Lua](http://www.lua.org/) is that it is very
fast. In fact a scripting language is fast it is _as fast as Lua_. Lua
is written in C and basically it has an API you can easilly extend to
fit to your needs.

The fact that it is written in C makes it possible to be used in Android
through the NDK.

## Primary Definitions

### NDK

The Android
[NDK](http://developer.android.com/tools/sdk/ndk/index.html) is a
toolset so you can write some of your code in C/C++. It should be
used in precise situations, not only because  you are a C guru.

#### Installation

Simply download the NDK
[here](http://developer.android.com/tools/sdk/ndk/index.html) and
unzip it where you want.

After that you need to update your path, using the following command :

    export PATH=$PATH:/where/you/put/the/ndk/android-ndk

The main command we will be using is `ndk-build` which allows us to
build NDK libraries.

### Using the NDK

To use the NDK, you have to put your C source file in the project's
jni directory. The file will be compile using the `Android.mk` files.

### Lua Java

#### Definition

LuaJava is a library that allows to make a bridge between Java and
[Lua](http://www.lua.org/), and because Android is Java, we can use it
in our android application.

#### Installation

The easiest way to add luajava to your android application is to
inspire from this very nice project :
[AndroLua](https://github.com/mkottman/AndroLua).

Simply copy the `jni` folder in your project and the `src\org` in
your src directory.

### Lua

### Some reading

To fully understand [Lua](http://www.lua.org/), I recommand you to
read the official book
[Programming in Lua](http://www.lua.org/pil/contents.html) which is
available online.

The part we wanna check really is the Fourth part of the book :
_The C API_

### C Api

When we say that [Lua](http://www.lua.org/) is an embedded language,
it means that it is simply a library that can interact with others,
supposing that you can write some interactions.

The C Api is basically a set of tools and function so we can make the
C interact with [Lua](http://www.lua.org/)

## Lets code

### Specifications

Because we are on a blog, I will not detail everything on this
article. If you have any question, put a comment about it.

Lets say for our example we will use Lua to script a character
generation. This is going to bevery simple. Lets define your
character class :

    public class ExampleCharacter() {
        private int id;
        private String name;
        private int attackPoints;
        private int defensePoints;
        private int lifePoints;

        // Getters and setters
        ...
    }

What we want to do is to generate a bunch of characters using
[Lua](http://www.lua.org/), and the following code :

    for i = 1, 2 do
        charac = charactEngine.new();
        charact.atP = random(1, 5);
        charact.deP = random(1, 5);
        charact.pV = random(5, 10);
    end

It is quite simply right? What we need to do is to create a
charactEngine class in [Lua](http://www.lua.org/) so we can generate
our characters in Java.

### The Code

### Java Side

Ok lets start by creating our charactEngine in Java. Lets make it a
singleton :

    public class CharactEngine {
        private static CharactEngine mInstance = new CharactEngine();
        // HashMap of the characters
        private HashMap<Integer, ExampleCharacter> mCharactMap;

        private CharactEngine() {
            this.mCharactMap = new HashMap<Integer, ExampleCharacter>();
        }

        public static getInstance() {
            return this.mInstance;
        }

        // This is the function that we will be calling to create
        public int createNew() {
            ExampleCharacter mCharac = new ExampleCharacter();
            mCharact.setId(String.valueOf(mCharactMap.size() + 1));
            mCharact.setName("toto_" + String.valueOf(mCharactMap.size() + 1));
            this.mCharactMap.put(
        }

So the `charactEngine.new()` is equivalent to :

    CharactEngine cEngine = CharactEngine.getInstance();
    cEngine.createNew();

### C Side

Ok now lets create our different objects. First we need to create the
`charactEngine` class. All those modification will be made in the
`jni/lua` directory

#### lualib.h

We first need to define what will be our new class. Lets open the
`lualib.h` file and add the following lines, like it is made in the
file :
{% raw %}
    #define LUA_LOADLIBNAME        "package"
    LUALIB_API int (luaopen_package) (lua_State *L);

    // Add these lines
    #define LUA_ENGINELIBNAME "charactEngine"
    LUALIB_API int (luaopen_engine) (lua_State *L);
{% endraw %}

What we did is we defined the class name and the function that will
load the class.

#### lcenginelib.c

Ok now we can develop the functions that we will have in our class :

{% raw %}
    #include <jni.h>
    #include <stdio.h>
    #include <stdlib.h>

    #define lcenginelib_c
    #define LUA_LIB

    #include "lua.h"

    #include "lauxlib.h"
    #include "lualib.h"

    static JNIEnv * getAppEnvFromState( lua_State * L ) {
        // Simply inspire from the one in lua java
    }

    static int cengine_new ( lua_State * L ) {
        // This is the function that we will be coding
    }

    /* This is the functions to register the library */
    static const luaL_reg cenginelib[] = {
    {"new", cengine_new},
    {NULL, NULL}
    };

    LUALIB_API int luaopen_cengine (lua_State *L) {
        luaL_register(L, LUA_ENGINELIBNAME, cenginelib);
        return 1;
    }
{% endraw %}

This allows us to register the class and its functions. (Just new for
this one). We will get back the the function  `cengine_new`.

#### linit.c

Finally we have to add the library to the loading phase of
[Lua](http://www.lua.org/). Simply find the `luaL_Reg lualibs` array
and modify it like this :

{% raw %}
    {LUA_APPLIBNAME, luaopen_app},
    {LUA_ENGINELIBNAME, luaopen_cengine},
    {NULL, NULL}
{% endraw %}

We simply add the library to the [Lua](http://www.lua.org/) loader.

Ok now we have everything we need to start interfacing Java and
[Lua](http://www.lua.org/)

#### Back to lcenginelib.c

Lets code the cengine_new function. First we need to add a few stuff :

    static jclass    App_api_class    = NULL;
    static jmethodID get_message_method   = NULL;
    // To get local ref
    static jclass    throwable_class      = NULL;
    // To get global ref
    static jclass mlClass = NULL;

Ok and now the function :

    static int engine_new ( lua_State * L ) {
        JNIEnv * javaEnv;
        jmethodID method;
        jobject mlObj;
        int mRet;

        // We get the JNI Env
        javaEnv = getAppEnvFromState( L );

        if ( javaEnv == NULL ) {
            // If there is an error
            lua_pushstring( L , "Invalid JNI Environment." );
            lua_error( L );
        }

        // Get the CharactEngine Java Class
        throwable_class = ( *javaEnv )->FindClass(javaEnv, "com/example/ExampleApp/model/CharactEngine");
        if (throwable_class == 0) {
            lua_pushstring( L , "FindClass error" );
            lua_error( L );
            return;
        }
        mlClass =  ( *javaEnv )->NewGlobalRef(javaEnv, throwable_class);
        ( *javaEnv )->DeleteLocalRef(javaEnv, throwable_class);

        // Retreive the method ID for the getInstance
        method = ( *javaEnv )->GetStaticMethodID( javaEnv , mlClass , "getInstance" ,"()Lcom/example/ExampleApp/model/CharactEngine;");
        if (method == 0) {
            lua_pushstring( L , "GetMethodID error" );
            lua_error( L );
            return;
        }

        // Check if we really have the class
        if ( mlClass == NULL || method == NULL ) {
            lua_pushstring( L , "Invalid method com.example.ExampleApp.model.CharactEngine.getInstance." );
            lua_error( L );
            return;
        }

        // Get the java object as a local ref and save it as a global ref
        mlObj = ( *javaEnv )->CallStaticObjectMethod(javaEnv, mlClass, method);
        mlGlobalObj = ( *javaEnv )->NewGlobalRef(javaEnv, mlObj);
        ( *javaEnv )->DeleteLocalRef(javaEnv, mlObj);

        method = ( *javaEnv )->GetMethodID( javaEnv , mlClass , "createNew" ,"()I");
        if (method == 0) {
            lua_pushstring( L , "GetMethodID error" );
            lua_error( L );
            return;
        }

        if ( mlClass == NULL || method == NULL ) {
            lua_pushstring( L , "Invalid method com.example.ExampleApp.CharactEngine.createNew." );
            lua_error( L );
            return;
        }

        // Call the create function
        mRet = ( *javaEnv )->CallObjectMethod(javaEnv, mlGlobalObj, method, jstr);
        // This is the new function
        create_new_entity(L, mRet);

        ( *javaEnv )->DeleteGlobalRef(javaEnv, mlClass);
        ( *javaEnv )->DeleteGlobalRef(javaEnv, mlGlobalObj);

        return 1;
    }

You are probably wondering what is the `create_new_entity`. Well the
thing is we did not create any entity in [Lua](http://www.lua.org/).
So we have to create a C file in which we define our
[Lua](http://www.lua.org/) entity which will manipulate our objects.
