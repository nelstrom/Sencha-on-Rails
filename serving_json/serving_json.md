!SLIDE

# SERVING JSON

!SLIDE commandline incremental

## JAVASCRIPT

    @@@javascript
    $ 42.toString()
    > "42"

    $ "42".toInt()
    > TypeError: Object 42 has no method 'toInt'

    $ parseInt("042", 10)
    > 42

!SLIDE commandline incremental

## RUBY

    @@@ruby
    $ 42.to_s
    => "42"

    $ "042".to_i
    => 42

!SLIDE commandline incremental

## Serialization

    @@@ruby
    $ car = {:make => "bmw", :year => "2003"}
    => {:make=>"bmw", :year=>"2003"}

    $ car.to_json
    => NoMethodError: undefined method `to_json'

    $ require 'json'
    => true

    $ car.to_json
    => '{"make":"bmw","year":"2003"}'

!SLIDE code small

## Rails controller

    @@@ruby
    def index
      @tasks = Task.all
      render :text => { :tasks => @tasks }.to_json
    end

.notes render :text/:file/:template/:partial

!SLIDE code small

## Rails controller

    @@@ruby
    def index
      @tasks = Task.all
      render :json => { :tasks => @tasks }
    end

!SLIDE commandline

    $ rails generate scaffold task

!SLIDE code small

    @@@ruby
    class TagsController < ApplicationController
      def index; end
      def show; end
      def new; end
      def create; end
      def edit; end
      def update; end
      def destroy; end
    end

!SLIDE code small

    @@@ruby
    # GET /tasks
    # GET /tasks.json
    def index
      @tasks = Task.all

      respond_to do |format|
        format.html # index.html.erb
        format.json {
          render :json => @tasks
        }
      end
    end
!SLIDE code small

    @@@ruby
    # GET /tasks
    # GET /tasks.json
    def index
      @tasks = Task.all

      respond_to do |format|
        format.html # index.html.erb
        format.json {
          render :json => { :tasks => @tasks }
        }
      end
    end


!SLIDE code small

    @@@coffeescript
    App.stores.tasks = new Ext.data.Store
      model: 'Task'
      autoLoad: true
      proxy:
        type: 'rest'
        url: '/tasks.json'
        reader:
          type: 'json'
          root: 'tasks'
