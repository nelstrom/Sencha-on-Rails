# CRUD

CRUD, as I'm sure you all know, stands for Create, Read, Update, Destroy. We've already dealt with the "R" bit, but unless you're happy to create a read-only API, then you'll want to try and stay awake for the next bit.

## Gotcha

As soon as you try to send data from Sencha to rails, you're going to run into a gotcha.

### Default layout

This is what the default rails template looks like. See that declaration that reads 'csrf_meta_tags'? When the template is rendered, it produces two meta tags, which are an important part of rails's security.

### Cross-site request forgery

CSRF stands for cross site request forgery. This is a page from the rails security guide, for which you'll be able to find a link in my slide deck.

CSRF is ... TODO!

In a regular HTML rails app, you don't have to give much thought to the measures that rails takes to protect your app from CSRF. So long as you always use the form helper tags, then rails will add a hidden field containing the csrf token to your forms.

But when we use javascript to render the forms on the client side, we have no access to the rails ecosystem, including the helpers for inserting csrf tokens into form fields. So we're going to have to find another way to do it.

### Override Ext.Ajax

Luckily, the fact that the token is included in the head of our DOM means that we can extract it, and inject that token into our AJAX requests.

In the Teado demo app, there's a file called overrides.js. Here, we hook into the `beforerequest` event of the base Ext.Ajax class, and create a header that includes the token. This singleton class is used for every Ajax request made by Sencha. So even when we submit a form using Ajax, this is called under the hood.

If you take one thing from this talk, remember this slide.

## CREATE

Now that our Sencha app includes a CSRF token with each AJAX call, we can send POST, PUT and DELETE requests that rails will trust. So lets look at how to create a new record.

Here's the form for creating a new Task. The code that generates this form is too long to fit on a slide, but lets focus on the interesting part: the create button handler.

### Create button handler

This does nothing more than to dispatch to the 'create' action of the Tasks controller. Note that in this context, the `this` keyword stands for the form itself. By including it when we dispatch to the controller, we create a reference that we can use inside of the controller action.

### Submitting the form

In the 'create' action, we simply submit the form, sending the request to the url `/tasks.json`.

### Definition of successful AJAX response

When we call the submit method, we can provide callbacks for success and failure. How does Sencha decide whether the request was successful? We simply have to return a JSON response including a `success: true` (*success is true*) parameter.

### Rails create action

So in our rails controller, we attempt to create a Task record using the parameters sent by the form. If it's saved successfully, we return a JSON response including the `success: true` parameter. For a failure, we'll instead return a JSON response of the errors on the new record.

### Client-side: showing errors

Here's the client side create action again, this time expanded to include a failure callback, which simply reveals any errors on the form.

And finally, here's what the form looks like after revealing errors. We don't have time to go over how this works, but I'll refer you instead to a this screencast about working with forms. You can find the link in my slidedeck.

## Routes

I'd just like to draw attention to the fact that the URL for fetching an index, and for creating a new record are identical. The only thing that differs between the two is the HTTP method. So if we send a GET request to slash-tasks-dot-json, Rails will return a list of tasks. Whereas if we POST to the same URL, Rails will attempt to build a new record from the parameters that we send.

In a similar fashion, the update and destroy actions are both addressed with the same URL. If we send a PUT request to, say, tasks-slash-1-dot-json, then rails will attempt to update the record with an id of 1. Whereas if we send a DELETE request to the same URL, rails will destroy the same record.

With that knowledge, we can now create our UPDATE and DESTROY actions in our Sencha tasks controller.

### UPDATE

The update action looks almost identical to the create action, except for a couple of details. First, we specify the URL including the id of the record that we want to modify. And we use a PUT instead of a POST request.

### DESTROY

The destroy action is very similar, except that use the DELETE method.

### Handlers

Just to complete the picture for you: here's the task update form. In the same way that the 'create' button handler dispatched to the 'create' action, the update and delete buttons each dispatch to the two controller actions we just examined.
