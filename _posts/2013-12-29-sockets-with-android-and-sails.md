---
layout: post
title: "Sockets with Android and Sails"
description: "Lets have fun with sockets on Android"
category: "web mobile"
tags: [sails android socket]
---
{% include JB/setup %}
## Introduction

Sockets are the new hyper now, especially because they are very easy to implement on NodeJS. Basically sockets are end points of a communication flow occuring on the network between two interfaces. It allows a continous communication between those two hence allowing new real time mecanism.

I am going to show you how to implement a simple communication using sockets between a native Android application and a sails backend.

## Preparation

First we need to create a new sails project :

    # sails new socketproject

And a new android project :

    # android create project --target <target-id> --name SocketApp -path <path-to-workspace>/SocketApp

But of course, you can create it through your favorite IDE.

In this article we will be using [socket.io](socket.io) which comes bundled with sails, both for client and server. But as we are going to use an Android client, we need to use an external library. I recommand [Socket.IO-Client](https://github.com/Gottox/socket.io-java-client) which works pretty well. To add it to your project, just do the following :

    # git clone git://github.com/Gottox/socket.io-java-client.git
    # cd socket.io-java-client
    # ant jar
    # mv jar/socketio.jar /path/SocketApp/libs/

Don't forget to include the library in your build path.

## Socket Creation

To create a socket on Android, we need to instanciate a `SocketIO` object,  connect it to an url and provide it a `IOCallback` interface to handle the communication. So in your activity :

    public class SocketActivity extends Activity {
        ...
        @Override
        public void onStart() {
            // I'd rather do it onStart we the activity is launched and visible
            super.onStart();
            SocketIO socket = null;
            try {
                socket = new SocketIO();
                socket.connect(new URL("http://yourip:1337/"), new IOCallback() {
                    @Override
                    public void on(String event, IOAcknowledge ack, Object... args) {
                        Log.e("TEST", "EVENT");
                        if ("connection-accepted".equals(event)) {
                            Log.e("SocketIO server answered back");
                        }
                    }
                    @Override
                    public void onMessage(JSONObject json, IOAcknowledge ack) {
                        Log.e("TEST", "MESSAGE");
                    }
                    @Override
                    public void onMessage(String data, IOAcknowledge ack) {
                        Log.e("TEST", "MESSAGE");
                    }
                    @Override
                    public void onError(SocketIOException socketIOException) {
                        Log.e("TEST", "ERROR : " + socketIOException.getMessage());
                    }
                    @Override
                    public void onDisconnect() {
                        Log.e("TEST", "DISCONNECTED");
                    }
                    @Override
                    public void onConnect() {
                        Log.e("TEST", "CONNECTED");
                    }
                });
            } catch (MalformedURLException e) {
                e.printStackTrace();
            }
        }

The example is pretty self explanatory, the interesting thing is the callback, it defines every method to communicate with the `socket.io` server. To check if my connection went fine, I defined an action to execute when the server answer back.

Now in sails to define this action, in the `config/sockets.js` I simply modify the `onConnect` function to :

    ...
    onConnect: function(session, socket) {
        socket.emit("connection-accepted", {});
    }
    ...

Ok now lets launch both our application and our server and check what happen in the logs of the application ... **Nothing**

## Correction

It is not working because when a client wants to establish a persistant real time connection with `socket.io`, it needs to start a handshaking process. Basically it means one needs to have cookies to be identified when one connects through a third party.

To establish such a thing, you need to initiate the handshake you need to make a first request to the sails server and gather the cookies using the `CookieManager` provided by the android SDK.

**NB :** It is a good thing ! With this you can always know who is currently connected to your app.

Ok so lets define a basic http call. First on the serveur. The route :

    // routes.js
    ...
    "/android/call", "main.androidCall"

Then we generate the controller and implement the action :

    # sails generate controller main
    ...
    // MainController.js
    ...
    module.exports {
        androidCall: function(req, res) {
            res.json({
                status: 100
            });
        }
    }

And that's it. Quite easy.

Now in android, we need to make this call. Lets create an Asynctask for that and use the power of the cookie manager :

    public class SocketActivity extends Activity {
        @Override
        public void onStart() {
            super.onStart()
            AndroidCall androidCall = new AndroidCall();
            androidCall().execute("http://youip:1337/android/call");
        }
        ...
        public class AndroidCall extends AsyncTask<String, Integer, Boolean> {
            @Override
            public Boolean doInBackground(String... params) {
                HttpURLConnection mlUrlConnection = null;
                URL mlUrl;
                try {
                    mlUrl = new URL(params[0]);
                    mlUrlConnection = (HttpURLConnection) mlUrl.openConnection();
                    // Set cookies in requests
                    CookieManager cookieManager = CookieManager.getInstance();
                    String cookie = cookieManager.getCookie("http://yourip:1337");
                    if (cookie != null) {
                        mlUrlConnection.setRequestProperty("Cookie", cookie);
                    }
                    Log.e("TEST", "OPENING CONNECTION");
                    // Get cookies from responses and save into the cookie manager
                    List<String> cookieList = mlUrlConnection.getHeaderFields().get("Set-Cookie");
                    if (cookieList != null) {
                        for (String cookieTemp : cookieList) {
                            cookieManager.setCookie(mlUrlConnection.getURL().toString(), cookieTemp);
                        }
                    }
                    // Read the response
                    int status = mlUrlConnection.getResponseCode();
                    switch (status) {
                        case 200:
                        case 201:
                            return true;
                    }
                } catch (MalformedURLException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                } finally {
                    if (mlUrlConnection != null)
                        mlUrlConnection.disconnect();
                }
                return false;
            }
            @Override
            onPostExecute(Boolean aBoolean) {
                if (aBoolean)
                    connectSocket();
            }
        }
        public void connectSocket() {
            SocketIO socket = null;
            try {
                CookieManager cookieManager = CookieManager.getInstance();
                String cookie = cookieManager.getCookie("http://yourip:1337");
                socket = new SocketIO();
                socket.addHeader("Cookie", cookie);
                socket.connect(new URL("http://yourip:1337/"), new IOCallback() {
                    @Override
                    public void on(String event, IOAcknowledge ack, Object... args) {
                        Log.e("TEST", "EVENT");
                        if ("connection-accepted".equals(event)) {
                            Log.e("SocketIO server answered back");
                        }
                    }
                    @Override
                    public void onMessage(JSONObject json, IOAcknowledge ack) {
                        Log.e("TEST", "MESSAGE");
                    }
                    @Override
                    public void onMessage(String data, IOAcknowledge ack) {
                        Log.e("TEST", "MESSAGE");
                    }
                    @Override
                    public void onError(SocketIOException socketIOException) {
                        Log.e("TEST", "ERROR : " + socketIOException.getMessage());
                    }
                    @Override
                    public void onDisconnect() {
                        Log.e("TEST", "DISCONNECTED");
                    }
                    @Override
                    public void onConnect() {
                        Log.e("TEST", "CONNECTED");
                    }
                });
            } catch (MalformedURLException e) {
                e.printStackTrace();
            }
        }
    }

In this example, we use the `CookieManager` to help retreive any existing cookie, we then make the connection to get any cookie and then because the `CookieManager` retains our cookies, we simply pass them to use to the socket.

## Conclusion

As you can see, it is pretty straighforward to implements a socket communication between a sails server and an android client. As far as I know from my colleague, it is has simple to do with IOS, simply use the same mecanism and use the corresponding library.

Hope you had a nice reading.
