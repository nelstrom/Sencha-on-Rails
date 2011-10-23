# The Asset Pipeline

The asset pipeline is relatively new in rails. The first version shipped with Rails 3.1, at the end of August this year.

The asset pipeline is a set of technologies and conventions for managing the scripts, styles and images required by our rails apps. It makes it super easy to use templating languages that compile into CSS or javascript.

## Compilation

I don't think SASS requires any introduction to a room full of Sencha developers, since it's the backbone of theming in both ExtJS and Sencha Touch.

Coffeescript is a little language that compiles into javascript. I have to admit, coffeescript has stolen my heart. And I apologise to any doubters or haters, but my demo app, and all of the code samples in this demonstration are in coffeescript. We'll see what it looks like shortly.

Now, I just want to stress that it has always been possible to use these technologies with Rails. But previously, you would typically have to open up a different terminal session to launch a process for each compiler. The asset pipeline wraps each of these technologies into the rails server, so we only have to launch a single process.

## Development mode

In development mode, each script and stylesheet is loaded into the document indivually, which makes it easy to debug.

## Production mode

In production mode, all javascript resources are concatenated into a single script, and the same goes for stylesheets. This minimizes the number of HTTP requests, which is a best practice for making fast loading pages. Note too that the filenames include a fingerprint. This ensures that browser caching doesn't cause old stylesheets to be served after a fresh deployment.

# Loading Sencha frameworks

The asset pipeline has a handful of directories in its load path. If we place a javascript file in any of these directories, then we can reference it by name, and it will be loaded into our HTML page.

* app/assets is for scripts and styles that define the rails application
* vendor/assets is for any 3rd party plugins, so this is an ideal place to put the Sencha Touch libraries
* lib/assets is for... well, I'm not sure to be honest!

So if we create a `vendor/assest/javascripts` directory, we can place the sencha-touch js library in there. And if we create a `vendor/assets/stylesheets` directory, we can put the Sencha Touch stylesheet in there.

But how do we load these into our application? Well, in our app/assets/stylesheets directory, we can create a `touch.css` file, and add a directive that references the Sench Touch theme.

Note that this is a regular CSS file, but the `require` directive has a special meaning. A part of the asset pipeline called *sprockets* uses this directive to fetch the referenced file, by looking for it in the load path.

Here, we're loading the pre-compiled version of the Sencha Touch theme. Later, I'll show you how to compile it dynamically using compass.

We can load the sencha touch javascript libraries in the same manner. This time, as well as loading the library, we also want to load the files that define our own application: starting with the routes, app, and an overrides file.

The require_tree directive doesn't just load a single file, it recursively loads all of the javascript files in a specified directory. If we load the models, stores, views then controllers in that order, then everything should work smoothly. Note the leading dot in these paths: the require_tree directive expects a relative path.

## Conventions

By rails convention, the MVC components of an app are each placed in a directory below the `app` directory.

Sencha follows a similar convention.

But the asset pipeline specifies that javascript files should be placed under `app/assets/javascripts`. So if we combine these rules, we end up with this directory structure. The rails `app` directory contains the sencha `app` directory.

Now, when I look at this, I get the impression that the rails MVC components are somehow more important, while the Sencha MVC components are second class, because you have to dig through several directories to find them.

If this bothers you, I suggest creating a symlink (or alias, if you prefer to call it that). We can't have two `app` directories in the root of our project, so I call the Sencha directory `touch`, and link it to the conventional directory.

Rather than drilling though several sets of directories, we can now find the Sencha Touch MVC components at the same level as those for Rails. This is by no means an essential step, but I find it convenient.

## Theming - compass integration

As I'm sure you already know, Sencha Touch is themed using Sass and compass. If you're unsure what the difference is between Sass and compass, then it helps to think of Sass as being a language, like ruby or javascript, and compass as being a framework, like rails or sencha touch.

The rails asset pipeline supports sass out of the box, but sadly, compass is not enabled by default. It's not difficult to add support though. Simply add the line:

    gem 'compass'

to your Gemfile, and run `bundle install`.

What with the asset pipeline being relatively new, there's still quite a lot of movement in this space. The latest stable release of compass does not support the rails asset pipeline, so right now, you have to add these lines to install an alpha version of the next release. If you're watching this presentation on video, check to see what the current status is.

We also need to install the Sencha Touch compass theme. This comes bundled with the SDK when you download the Sencha Touch framework, but the easiest way to install it into a rails project is as a gem. I've taken the liberty of creating a gem out of the Sencha Touch theme, so you can install it into your rails app straight from github by adding this line to your Gemfile.


