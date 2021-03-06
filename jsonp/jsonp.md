!SLIDE

# Backing a standalone app

!SLIDE center


[![](../images/same-origin-policy.png)](https://developer.mozilla.org/en/Same_origin_policy_for_JavaScript)

!SLIDE center

![](../images/jsonp-proxy.png)

!SLIDE small

## Serving JSON from rails

    @@@ruby
    def index
      @tags = Tag.all
      render :json     => { :tags => @tags}

    end

!SLIDE small

## Serving JSON*P* from rails

    @@@ruby
    def index
      @tags = Tag.all
      render :json     => { :tags => @tags},
             :callback => params[:callback]
    end

!SLIDE bullets incremental

## content-types

* JSON - `application/json`
* JSONP - `application/javascript`

!SLIDE small code

    @@@ruby
    class TouchController < ActionController::Base

      before_filter :jsonp_content_type

      private
      
      def jsonp_content_type
        if !params[:callback].blank?
          response.content_type = "text/javascript"
        end
      end

    end

!SLIDE small

.notes including callback on all json responses is hard!

    @@@ruby
    def index
      @tags = Tag.all
      render :json     => { :tags => @tags},
             :callback => params[:callback]
    end

!SLIDE

# Rack Middleware

!SLIDE center

![](../images/middleware-1.png)

!SLIDE center

![](../images/middleware-2.png)

!SLIDE center

![](../images/middleware-3.png)

!SLIDE

## Using middleware to serve JSONP

!SLIDE center

[![](../images/rack-contrib.png)](https://github.com/rack/rack-contrib)

!SLIDE center

[![](../images/rack-contrib-readme.png)](https://github.com/rack/rack-contrib#readme)

!SLIDE code small


    @@@ruby
    # Gemfile
    gem 'rack-contrib'



    # config/application.rb
    require 'rack/contrib'

    module Teado
      class Application < Rails::Application
        # ...
        config.middleware.use Rack::JSONP
        # ...
      end
    end

!SLIDE

## SET IT
### &
## FORGET IT

[origin]: https://developer.mozilla.org/en/Same_origin_policy_for_JavaScript
[s]: http://docs.sencha.com/ext-js/4-0/#!/api/Ext.data.proxy.JsonP
[mid]: https://github.com/robertodecurnex/rack-jsonp-middleware
[contrib]: https://github.com/rack/rack-contrib

!SLIDE

### QUESTIONS
# ?
