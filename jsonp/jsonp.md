!SLIDE

# Backing a standalone app

!SLIDE 

## Same origin policy

prevents a document or script loaded from one origin from getting or setting properties of a document from another origin

!SLIDE center

![](../images/jsonp-proxy.png)

!SLIDE small

## Serving JSON from rails

    @@@ruby
    def index
      @tags = Tag.all
      render :json => { :tags => @tags} 
    end

!SLIDE small

## Serving JSONP from rails

    @@@ruby
    def index
      @tags = Tag.all
      render :json     => { :tags => @tags},
             :callback => params[:callback]
    end

!SLIDE

## MIME types

* JSON - `application/json`
* JSONP - `text/JavaScript`

!SLIDE small code

    @@@ruby
    class TouchController < ActionController::Base

      before_filter :customize_content_type

      private
      
      def customize_content_type
        if params[:callback]
          response.content_type = "text/JavaScript"
        end
      end

    end

!SLIDE

## Further reading

* Sencha's [JsonP proxy][s]

[s]: http://docs.sencha.com/ext-js/4-0/#!/api/Ext.data.proxy.JsonP
