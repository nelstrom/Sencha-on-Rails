!SLIDE

# CRUD

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

[![](../images/csrf-rails-guide.png)](http://guides.rubyonrails.org/security.html#cross-site-request-forgery-csrf)

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

!SLIDE bullets
