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

## touch/controllers/Sessions.js.coffee

    @@@coffeescript
    Ext.regController 'Sessions',

      create: (params) ->
        params.form.submit(

          success: ->
            Ext.dispatch
              controller: 'dashboard'
              action: 'index'

          failure: (form, result) ->
            form.showErrors(result)

        )

!SLIDE smaller code

## touch/stores/CurrentUser.js.coffee

    @@@coffeescript
    App.stores.currentUser = new Ext.data.Store(
      fields: [
        {name: 'active',   type: 'boolean'}
        {name: 'username', type: 'string'}
      ]
    )


!SLIDE smaller code

## touch/controllers/Sessions.js.coffee

    @@@coffeescript
    success: (form, response) ->
      App.stores.currentUser.removeAll()
      App.stores.currentUser.add(response.current_users[0])

      Ext.dispatch
        controller: 'dashboard'
        action: 'index'

!SLIDE smaller code

## touch/views/Dashboard.js.coffee

    @@@coffeescript
    App.views.Dashboard = Ext.extend Ext.Panel,

      initComponent: () ->

        Ext.apply this,
          scroll: 'vertical'
          tpl: 'Welcome, {username}!'
          styleHtmlContent: true
          listeners:
            beforeactivate: ->
              if user = App.stores.currentUser.first()
                @update(user.data)

!SLIDE

## DEMO

!SLIDE

* [Snapshot on github][01]

[01]: http://github.com/nelstrom/Teado

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

!SLIDE smaller

    @@@html
    <!DOCTYPE html>
    <html>
    <head>
      <title>Teado</title>
      <link href="/assets/sencha-touch.css"/>
      <script src="/assets/sencha-touch.js"></script>
      <script src="/assets/app.js"></script>

      ...

      <meta name="csrf-param"
        content="authenticity_token"/>

      <meta name="csrf-token"
        content="8Yfi73UlA9PYPMWFCdiMlcMpHYT+/5Wf+4BIE0tmyf0="/>

    </head>
    <body></body>
    </html>

!SLIDE center

![](../images/csrf-rails-guide.png)

!SLIDE smaller code

## touch/overrides.js.coffee

    @@@coffeescript
    Ext.Ajax.on('beforerequest',
      (connection, options) ->
        token = Ext.select('meta[name="csrf-token"]')
        if token.first()?
          content = token.first().dom.content
          options.headers ||= {}
          options.headers["X-CSRF-Token"] = content
      this
    )

!SLIDE

## Further reading

* [Rails authentication plugins][toolbox]
* [Authentication from scratch][auth]
* [Securing rails applications][csrf]

[csrf]: http://guides.rubyonrails.org/security.html#cross-site-request-forgery-csrf
[auth]: http://railscasts.com/episodes/250-authentication-from-scratch
[toolbox]: http://ruby-toolbox.com/categories/rails_authentication.html
