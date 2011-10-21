# Serving JSON

## Casting in JavaScript

In JS, there's a `toString()` method that we can call on an integer, which returns the number formatted as a string.

But if we try and call `toInt()` on a string, JS throws an error.

Instead, we have to pass the string as an argument to a global function, called `parseInt()`.

This is bad design, and personally, I have trouble remembering what works and what doesn't.

## Casting in Ruby

Ruby behaves more predictably.  We can call `to_s` on an integer and it returns a string. Or we can call `to_i` on a string, and ruby will attempt to return an integer.

## Serialization

In ruby, this basic data structure is called a hash. The syntax is different, but it's roughly equivalent to python's dictionaries, and javascripts objects.

In an ideal world, we could just call `to_json` on a ruby hash, and have it return a string representing the same data structure in JSON notation. Alas, ruby throws an exception.

Come on ruby! This is almost as bad as having to work with JS!

Now watch this: if we load the json module from the standard library, then try again...

Boom!

## Rails controller

If we want to serve JSON from a rails controller, we hardly have to do anything. Here, we're fetching all Task models, placing them inside a hash, then casting it to JSON. But even this is more work than necessary!

If we tell rails to render JSON, then it will automatically call the to_json method on whatever object we give it.

## Scaffold controller

If we run this command in the shell, rails generates a task model for us, along with a controller and the 7 basic actions required to manage that model.

Lets zoom into the index action.

### The tasks#index action

First, note the comment at the top. It indicates that this action is triggered by a GET request to the slash-tasks path with or without the dot-json extension.

This respond_to block does a bit of magic. It allows us to specify different responses, depending on whether or not the request included the json extension.

Let's throw away the HTML response for now. We're only interested in serving an API, so it's just clutter.

What about this JSON response. The @tasks instance variable references an array of all existing task models.

Now, we could cast that to JSON as is, but let's wrap it up inside of a hash, so that we can label what's in the list.

### The Ext data store

On the client side, we can set up an Ext data store that will happily consume this response. Notice that we've set the URL to slash-tasks-dot-json, and told the reader to look for a key called tasks.

### The JSON response

Lets have a look at an example response. By default, when rails converts a model to JSON, it creates a key value pair for every attribute.

What if we wanted to include *everything but* the updated_at and created_at values?

### Blacklist

If we open up the model class in rails, we can override the `as_json` method. Not that it is not `to_json` this time, but AS json. Both methods exist, but to_json deals with the actual serialization, whereas as_json prepares the hash to be serialized.

Using the `:except` property, we can supply a blacklist of attributes that we want to exclude when generating JSON.

This modifies the JSON response to look like this.

### Whitelist

Alternatively, suppose that we wanted to include the id and name fields only. Instead of using `:except`, we can do this using the `:only` property.

And here's the resulting JSON response.

The as_json method allows us to do all sorts of other interesting things when we serialize our models to JSON. Lets look at a more complex example.

## Associations

In the Teado demo app, each task can be tagged multiple times. Tags, also, can be be associated with multiple tasks. So what we have here is a many to many relationship.

### Associations in Rails

In rails, we can set up these associations be adding the `has_and_belongs_to_many` declaration to each model, referencing the other.

Now, you probably already know that to create a many to many relationship in a relational database, we have to use a join table. That is, a table containing two columns, each referencing one of the models. We could name this table however we like, but rails has opinions about how it should be done.

Rails says the join table should include both model names, in plural form, and that they should appear in alphabetical order. So in this case, that means tags_tasks. It wouldn't be the end of the world if we named the table differently, but then we would have to specify the table name when we set up the relationship. Rails is all about convention over configuration, and if you choose to fly against it then so help you.

This, by the way, is a database migration file. When we execute it, it will create the specified table in our database.

### Including associations in JSON

Remember earlier on, I said that there were more advanced ways that we could use the `as_json` method? Well, here's a good example. We're still using the `:only` property to specify a whitelist of model attributes. But this time, we're also telling it to `:include` any associated tags.

And here's the resulting JSON response. Pretty nifty, right?

### Associations in Sencha

Now, we have to make sure that Sencha Touch can interpret this nested data. It's just as easy as it was in rails. We set up the association on the Task model by declaring that it hasMany tags.

And that's it. If we load the nested JSON into the Tasks store, then each instance of the task model will acquire a `.tags()` method, which returns a Store containing the nested data.

I hope that gives you some insight into how well the Sencha and Rails stacks play together. Rails serves JSON. And Sencha consumes it. Both frameworks do a great job of abstracting the implementation details out of site. And in both cases, you're left to deal with an elegant API.
