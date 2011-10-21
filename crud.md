# CRUD

CRUD, as I'm sure you all know, stands for Create, Read, Update, Destroy. We've already dealt with the "R" bit, but unless you're happy to create a read-only API, then you'll want to try and stay awake for the next bit.

## Gotcha

As soon as you try to send data from Sencha to rails, you're going to run into a gotcha.

### Default layout

This is what the default rails template looks like. See that declaration that reads 'csrf_meta_tags'? When the template is rendered, it produces two meta tags, which are an important part of rails's security.

### Cross-site request forgery

CSRF stands for cross site request forgery. This is a page from the rails security guide, for which you'll be able to find a link in my slide deck.

CSRF is ... TODO!

In a regular HTML rails app, you don't have to give much thought to the measures that rails takes to prevent your app from CSRF. So long as you always use the form helper tags, then rails will add a hidden field containing the csrf token to your forms.

But when we use javascript to render the forms on the client side, we have no access to the rails ecosystem, including the helpers for inserting csrf tokens into form fields. So we're going to have to find another way to do it.

### Override Ext.Ajax

Luckily, the fact that the token is included in the head of our DOM means that we can extract the it, and inject that token into our AJAX requests.

In the Teado demo app, there's a file called overrides.js. Here, we hook into the `beforerequest` event of the base Ext.Ajax class, and create a header that includes the token. This singleton class is used for every Ajax request made by Sencha. So even when we submit a form using Ajax, this is called under the hood.

If you take one thing from this talk, remember this slide.
