# Responders

A set of responders modules to dry up your Rails 3 app. If you are interested in the version
to be used with Rails 2.3 together with Inherited Resources, please check this branch instead:

http://github.com/plataformatec/responders/tree/v0.4

## Responders Types

### FlashResponder

Sets the flash based on the controller action and resource status.
For instance, if you do: `respond_with(@post)` on a POST request and the resource `@post`
does not contain errors, it will automatically set the flash message to
`"Post was successfully created"` as long as you configure your I18n file:

```yaml
  flash:
    actions:
      create:
        notice: "%{resource_name} was successfully created."
      update:
        notice: "%{resource_name} was successfully updated."
      destroy:
        notice: "%{resource_name} was successfully destroyed."
        alert: "%{resource_name} could not be destroyed."
```

In case the resource contains errors, you should use the failure key on I18n. This is
useful to dry up flash messages from your controllers. If you need a specific message
for a controller, let's say, for `PostsController`, you can also do:

```yaml
  flash:
    posts:
      create:
        notice: "Your post was created and will be published soon"
```

This responder is activated in all non get requests. By default it will use the keys
`:notice` and `:alert`, but they can be changed in your application:

```ruby
config.responders.flash_keys = [ :success, :failure ]
```

You can also have embedded HTML. Just create a `_html` scope.

```yaml
  flash:
    actions:
      create:
        alert_html: "<strong>OH NOES!</strong> You did it wrong!"
    posts:
      create:
        notice_html: "<strong>Yay!</strong> You did it!"
```

### HttpCacheResponder

Automatically adds Last-Modified headers to API requests. This
allows clients to easily query the server if a resource changed and if the client tries
to retrieve a resource that has not been modified, it returns not_modified status.

### CollectionResponder

Makes your create and update action redirect to the collection on success.

## Configuring your own responder

The first step is instal responders gem and configure it in your application:

```console
gem install responders
```

In your Gemfile, add this line:

```ruby
gem 'responders'
```

Responders only provides a set of modules, to use them, you have to create your own
responder. This can be done inside the lib folder for example:

```ruby
# lib/app_responder.rb
class AppResponder < ActionController::Responder
  include Responders::FlashResponder
  include Responders::HttpCacheResponder
end
```

And then you need to configure your application to use it:

```ruby
# app/controllers/application_controller.rb
require "app_responder"

class ApplicationController < ActionController::Base
  self.responder = AppResponder
  respond_to :html
end
```

Or, for your convenience, just do:

```console
rails generate responders:install
```

## Controller method

This gem also includes the controller method `responders`, which allows you to cherry-pick which
responders you want included in your controller.

```ruby
class InvitationsController < ApplicationController
  responders :flash, :http_cache
end
```

## Generator

This gem also includes a responders controller generator, so your scaffold can be customized
to use `respond_with` instead of default `respond_to` blocks. Installing this gem automatically
sets the generator.

## Bugs and Feedback

If you discover any bugs or want to drop a line, feel free to create an issue on GitHub.

http://github.com/plataformatec/responders/issues

MIT License. Copyright 2012 Plataforma Tecnologia. http://blog.plataformatec.com.br
