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
    # GET /tasks.json
    def index
      @tasks = Task.all

      format.json {
        render :json => @tasks
      }
    end

!SLIDE code small

    @@@ruby
    # GET /tasks.json
    def index
      @tasks = Task.all

      format.json {
        render :json => { :tasks => @tasks }
      }
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

!SLIDE code smaller

    @@@javascript
    {
        "tasks": [
            {
                "assigned_to": 1,
                "completed_at": null,
                "due_at": null,
                "id": 1,
                "name": "Lorem ipsum dolor sit amet",
                "updated_at": "2011-10-14T18:02:25Z",
                "created_at": "2011-10-14T18:02:25Z",
            }
        ]
    }

!SLIDE code smaller

    @@@ruby
    class Task < ActiveRecord::Base
      def as_json
        super :except => [:updated_at, :created_at]
      end
    end

!SLIDE code smaller

    @@@javascript
    {
        "tasks": [
            {
                "assigned_to": 1,
                "completed_at": null,
                "due_at": null,
                "id": 1,
                "name": "Lorem ipsum dolor sit amet"
            }
        ]
    }

!SLIDE code smaller

    @@@ruby
    class Task < ActiveRecord::Base
      def as_json
        super :only => [:id, :name]
      end
    end

!SLIDE code smaller

    @@@javascript
    {
        "tasks": [
            {
                "id": 1,
                "name": "Lorem ipsum dolor sit amet"
            }
        ]
    }

!SLIDE

# MODEL ASSOCIATIONS

!SLIDE code smaller

## RAILS MODELS

    @@@ruby
    class Task < ActiveRecord::Base
      has_and_belongs_to_many :tags
    end

    class Tag < ActiveRecord::Base
      has_and_belongs_to_many :tasks
    end

!SLIDE code smaller

## JOIN TABLE

    @@@ruby
    class CreateTagsTasks < ActiveRecord::Migration
      def up
        create_table :tags_tasks, :id => false do |t|
          t.references :tag
          t.references :task
        end
      end
    end

!SLIDE code smaller

## RAILS MODELS

    @@@ruby
    class Task < ActiveRecord::Base

      has_and_belongs_to_many :tags

      def as_json
        super {
          :only    => [:id, :name],
          :include => :tags
        }
      end

    end

!SLIDE code smaller

    @@@javascript
    {
        "tasks": [
            {
                "id": 1,
                "name": "Lorem ipsum dolor sit amet",
                "tags": [
                    { "name": "latin" },
                    { "name": "placeholder" }
                ]
            }
        ]
    }

!SLIDE code smaller

## SENCHA MODEL

    @@@coffeescript
    App.models.Task = Ext.regModel 'Task',
      fields: [
        {name: 'id',       type: 'number'}
        {name: 'name',     type: 'string'}
      ]
      hasMany: { model: 'Tag', name: 'tags' }

!SLIDE commandline incremental

    @@@javascript
    $ task = App.stores.tasks.data.items[0]
    object

    $ task.name
    "Lorem ipsum dolor sit amet"

    $ tags = task.tags().data.items
    [ object, object ]

    $ Ext.each(tags, function(i) { console.log(i) })
    "Latin"
    "Placeholder"
