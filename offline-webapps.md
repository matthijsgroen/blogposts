Introduction
============

HTML 5 makes it possible to run your webapplications event when you're
offline. But how easy is it to create an app that makes use of this?

In this blogpost I will build a simple Rails 4 application, that
does some actions using HTML5 Offline web apps.

First we create a page that is available offline,
then we do an AJAX call that we will replace with an offline cachable
one. Than we will post information to the backend that, when offline
will wait till the app gets back online.

Lastly we will look into updating the HTML 5 application cache when our
webapplication updates and conflict resolving between the offline and
online data.

Setup Rails
-----------

Open your favorite terminall shell and we can start building

```sh
mkdir backbone-offline
cd backbone-offline
gem install rails --version=4.0.1
rails new .
```

Making a webpage offline
========================

```sh
rails generate controller welcome index
rails server
```

we edit the `config/routes.rb` to point the root to our new controller
action:

```ruby
  root 'welcome#index'
```

And time to edit the `app/views/welcome/index.html.erb` file.
You can place here what you want, I will place some text and headers
using HAML:

```haml
%h1 Welcome to the HTML 5 Offline test suite!

%p here we will test the following capabilities of HTML 5:
%ol
  %li Reaching a page without internet connection
  %li Do an AJAX call from Backbone.js when offline
  %li Submit data to the server when offline
  %li Update the application cache when the connection is restored
  %li look at various ways to do conflict resolvement

```

When the Rails server is started, and you point the browser to
`http://localhost:3000/` you see the page you just created the content
for. But when you kill the Rails server, and refresh the page, you get
an error page from your browser telling you that the remote server could
not be reached.

Time to change that!

To tell the browser that a webpage should be stored offline, you need to
add a manifest attribute to the html tag:

```haml
%html{ manifest: 'application.appcache' }
```

Now we need to serve this file from Rails, using the
`text/cache-manifest` mime-type.

in `config/routes.rb`

```ruby
get "/application.appcache" => proc { |env|
  [200, { 'Content-Type' => 'text/cache-manifest' }, [<<-APPCACHE.strip_heredoc]]
    CACHE MANIFEST
    # 2013-11-19T06:29

    CACHE:
    /
    /assets/application.js
    /assets/application.css

    NETWORK:
    *
  APPCACHE
}
```

to make sure we can use the plain urls for the assets, you can add the
following settings to `config/environment/deveopment.rb`

```ruby
  config.assets.debug = false

  # Minifiy our JS in development to have a single JS file to work with
  config.assets.js_compressor = :uglifier
  config.assets.compile = true

  # Remove the digest place the file without digest in the cache file
  config.assets.digest = false
```

Creating a front-end application
--------------------------------

Time to set up backbone.

```sh
git submodule add https://github.com/jashkenas/backbone.git vendor/assets/javascripts/backbone
git submodule add https://github.com/jashkenas/underscore.git vendor/assets/javascripts/underscore
```

This gets Backbone and its Underscore dependency right from the source.
Next I go into the vendor folders of each library and do a git checkout
of their latest release tag (Backbone 1.1.0 and Underscore 1.5.2 at time
of writing).



