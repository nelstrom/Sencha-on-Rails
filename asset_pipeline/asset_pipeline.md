!SLIDE

# The asset pipeline

!SLIDE

## The asset pipeline

* Rails 3.1 (released on [31 August, 2011][3.1])

.notes a new set of conventions + technologies for managing
.notes images, stylesheets, and javascripts

[3.1]: http://weblog.rubyonrails.org/2011/8/31/rails-3-1-0-has-been-released

!SLIDE

## COMPILATION

!SLIDE center

![Screenshot of the homepage for SASS](../images/homepage-sass.png)

!SLIDE center

![Screenshot of the homepage for Coffeescript](../images/homepage-coffeescript.png)

!SLIDE

## DEVELOPMENT MODE

!SLIDE smaller

    @@@html
    <!DOCTYPE html>
    <html>
    <head>
      <title>Teado</title>

      <link href="/assets/touch.css"/>

      <script src="/assets/sencha-touch.js"></script>
      <script src="/assets/app.js"></script>
      <script src="/assets/models/Task.js"></script>
      <script src="/assets/stores/Tasks.js"></script>
      ...

    </head>
    <body></body>
    </html>

.notes Dynamically include scripts/styles
.notes On demand compilation
.notes or 301 (not modified)

!SLIDE

## PRODUCTION MODE

!SLIDE smaller

    @@@html
    <!DOCTYPE html>
    <html>
    <head>
      <title>Teado</title>
      <link  href="/assets/touch-05210217.css"/>
      <script src="/assets/touch-8696bdd8.js"></script>
    </head>
    <body></body>
    </html>

.notes concatenation
.notes minification
.notes cache-busting fingerprints

!SLIDE

## LOADING SENCHA FRAMEWORKS
### + 
## YOUR APPLICATION

!SLIDE bullets

## LOAD PATHS

* app/assets/
* lib/assets/
* vendor/assets/

.notes app is for your application code
.notes vendor is for plugins
.notes lib is for ???

!SLIDE center

## Install Sencha Touch libs

![](../images/vendor-sencha.png)

.notes won't work for theming, but fine to get started

!SLIDE code small

## app/assets/stylesheets/touch.css

    @@@css
    /*
     *= require sencha-touch
    */

!SLIDE code small

## app/assets/javascripts/touch.js

    @@@javascript
    //= require sencha-touch
    //= require "app/routes"
    //= require "app/app"
    //= require "app/overrides"
    //
    //
    //
    //

!SLIDE code small

## app/assets/javascripts/touch.js

    @@@javascript
    //= require sencha-touch
    //= require "app/routes"
    //= require "app/app"
    //= require "app/overrides"
    //= require_tree "app/models"
    //= require_tree "app/stores"
    //= require_tree "app/views"
    //= require_tree "app/controllers"

!SLIDE 

# CRITICISM

!SLIDE

## SPROCKETS

### one size does not fit all

!SLIDE bullets incremental

## EXISTING SOLUTIONS

* Compass
* CommonJS
* npm

[CommonJS]: http://alexmaccaw.co.uk/posts/rails_js_packaging
[nodeify]: https://github.com/dkastner/nodeify
[stitch-rb]: https://github.com/maccman/stitch-rb