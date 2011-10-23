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


