!SLIDE

# Authentication

!SLIDE center

![A list of plugins for rails authentication](../images/rails-authentication-toolbox.png)

!SLIDE center

![Railscasts episode 250](../images/railscasts-auth.png)

!SLIDE smaller code

## config/routes.rb

    @@@ruby
    Teado::Application.routes.draw do
      get "touch"  => "touch#index"
      get "logout" => "sessions#destroy", :as => "logout"
      get "login"  => "sessions#new",     :as => "login"
      get "signup" => "users#new",        :as => "signup"
      resources :users
      resources :sessions
    end

!SLIDE smaller code

## app/models/user.rb

    @@@ruby
    class User < ActiveRecord::Base
      
      def self.authenticate(email, password)
        if user = find_by_email(email)
          secret = BCrypt::Engine.hash_secret(
                     password, user.password_salt)

          if user.password_hash == secret
            user
          end
        end
      end
      
    end

!SLIDE smaller code

## app/controllers/sessions_controller.rb

    @@@ruby
    def create
      user = User.authenticate(params[:email],
                               params[:password])

      if user
        session[:user_id] = user.id

        render :json => {
          :success => true,
          :current_users => [{
            :username => user.username,
            :active => true
          }]
        }, :status => :created

      else
        # ...
      end
    end

!SLIDE

    @@@javascript
    {
      "success" : true,
      "current_users": [{
        "username" : "drew",
        "active"   : true
      }]
    }

!SLIDE smaller code

## app/controllers/sessions_controller.rb

    @@@ruby
    def create
      user = User.authenticate(params[:email],
                               params[:password])

      if user
        # ...
      else
        render :json => {
          :success => false,
          :message => "Invalid login or password"
        } 
      end
    end


!SLIDE smaller code

## touch/views/LoginForm.js.coffee

    @@@coffeescript
    App.views.LoginForm = Ext.extend App.views.CommonForm,
      initComponent: ->
        Ext.apply(this,
          url: '/sessions.json'

          formFields: [
            {
              name : 'username'
              label: 'username'
            }
            {
              name: 'password'
              label: 'password'
              xtype: 'passwordfield'
            }
          ]
        )

!SLIDE smaller code

## app/controllers/users_controller.rb

    @@@ruby
    def create
      @user = User.new(params[:user])
      if @user.save
        redirect_to root_url, :notice => "Signed up!"
      else
        render "new"
      end
    end


!SLIDE 

## GOTCHA!

!SLIDE small

## app/views/layouts/touch.html.erb

    @@@html
    <!DOCTYPE html>
    <html>
    <head>
      <title>Teado</title>
      <%= stylesheet_link_tag    "touch" %>
      <%= javascript_include_tag "touch" %>
      <%= csrf_meta_tags %>
    </head>
    <body>

    <%= yield %>

    </body>
    </html>

!SLIDE

## Further reading

* [Rails authentication plugins][toolbox]
* [Authentication from scratch][auth]
* [Securing rails applications][csrf]

[csrf]: http://guides.rubyonrails.org/security.html#cross-site-request-forgery-csrf
[auth]: http://railscasts.com/episodes/250-authentication-from-scratch
[toolbox]: http://ruby-toolbox.com/categories/rails_authentication.html
