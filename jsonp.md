# Backing a standalone app

So far, we've assumed that our Sencha app is hosted on the same domain as the API supplied by rails. But what if that's not the case? Suppose that you want to deploy the front and back end on different domains. In particular, this would be the case if you were wrapping your Sencha Touch app inside PhoneGap, or using the packaging that comes with Sencha Touch 2.

## Same origin policy

In this scenario, we're going to run into problems because of the same origin policy. This prevents us from requesting JSON documents that are hosted on a different domain from the one that our script is hosted on.

However, there is a loophole in this restriction. We can load JavaScript from another domain, so long as it is referenced by a script tag. There are two steps to this: one on the server side, and one on the client side. Fortunately, both Sencha and Rails make it very straightforward to do.

## JSONP proxy

In Sencha, we just have to use a script tag proxy, which is also called a JSONP proxy. The 'p' stands for padded, because the JSON document is wrapped with an executable callback.

## Rails

Here's a rails controller serving vanilla JSON. We've seen this before. All that we have to do to turn this into a JSONP response, is to add one more parameter called 'callback'. If we make a request without a callback parameter, then rails will continue to serve vanilla JSON just as before. But if we include a callback parameter in the request, then rails will return JSONP using the specified callback.

Is that all there is to it?

## Content types

No. Not quite. One further complication arrises from the fact that JSON is supposed to be served with a content-type of application/json, whereas JSONP is supposed to be served with the content-type of application/javascript.

Fortunately, we don't have to test for this in every single controller action. Instead, we can use a before filter. This method will be executed before every action in the controller, so we can use it to set up the appropriate content type.

So really, the only thing we have to remember is to include this callback parameter in every controller action that serves JSON.

As solutions go, it seems simple. But the fact that you have to add this extra parameter to every single JSON-serving action makes it a tad error-prone. If you forget once, then that's game over for that particular action.

For a better solution, we'll look to Rack.

## Rack middleware

Rack is a ruby webserver interface. Rails is compatible with rack, but so too are other ruby web frameworks including Sinatra, and Goliath. So if you write a plugin for Rack, then it can be used in combination with any of these web frameworks. Rack plugins are called 'middlewares'.

Normally, when rails serves a page, we can say that the browser makes an HTTP request, and the server returns an HTTP response.

As the name suggests, middleware sits in between the client and server. 

For each request, the middleware can either modify it and send it on, block it entirely, or simply send it along unchanged. It can do the same with the response.

Every single request and response is filtered through the middleware, which makes it an ideal solution for serving JSONP. We simply inspect each request, to see if it includes a callback parameter. If it does, we strip it away, and forward the request on to Rails. Rails doesn't have to know if JSONP was requested. It simply returns JSON every time. But when the response comes back from Rails, the middleware takes care of wrapping it with the specified callback.

So the client will always receive JSONP when it requests it, and the Rails developer needn't worry about including the callback parameter in every single action.

## Set it, forget it

This is an ideal solution. You can set it up, then forget about it.

## Using middleware to serve JSONP

Dozens of Rack middleware plugins are available. There's a project called `rack-contrib`, which includes a collection of open source middlewares. This text is probably too small for you to read, but at the very top of the list, there's one for serving JSONP. As with all my links, you can get this one from my slidedeck.

## Setup

Installing this into your rails app is straightforward. Simply add 'rack-contrib' to your Gemfile. Then instruct rails to use the Rack::JSONP middleware on start up. You'll need to run `bundle update`, then restart the rails server for these changes to kick in, but then you can forget all about it.
