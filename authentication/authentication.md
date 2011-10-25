!SLIDE

# Authentication

!SLIDE center

[![A list of plugins for rails authentication](../images/rails-authentication-toolbox.png)](https://www.ruby-toolbox.com/categories/rails_authentication)

!SLIDE center

[![Railscasts episode 250](../images/railscasts-auth.png)](http://railscasts.com/episodes/250-authentication-from-scratch)

!SLIDE smaller code

## touch/stores/CurrentUser.js.coffee

    @@@coffeescript
    App.stores.currentUser = new Ext.data.Store
      fields: [
        {name: 'active',   type: 'boolean'}
        {name: 'username', type: 'string'}
      ]
      autoload: true
      proxy:
        url: 'users/current.json'

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

!SLIDE center

![](../images/welcome-message.png)
