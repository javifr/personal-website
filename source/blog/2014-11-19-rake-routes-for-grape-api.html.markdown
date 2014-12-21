---
title: Rake routes for Grape Api
date: 2014-11-19 13:06 UTC
tags: notes, order
comments: on
---

# Rake routes for Grape

If you are a [Rails](http://rubyonrails.org/) developer using [Grape](https://github.com/intridea/grape) you'll miss a command similar to ```rake routes``` but listing the Grape generated routes. In order to have the same functionallity I've build a little rake task.

In your rails ( or rails-api ) project create this file **/lib/tasks/grape.rake** with this content:

~~~ruby
namespace :grape do
  desc "routes"
  task :routes => :environment do
    API::Root.routes.map { |route| puts "#{route} \n" }
  end
end
~~~


Then, you can call it inside your project like this:

~~~bash
rake grape:routes
~~~

And this could be a sample output:

~~~shell
version=v1, method=POST, path=/customers(.:format)
version=v1, method=GET, path=/customers(.:format)
version=v1, method=GET, path=/customers/:id(.:format)
version=v1, method=PATCH, path=/customers/:id(.:format)
version=v1, method=GET, path=/rewards(.:format)
version=v1, method=POST, path=/rewards(.:format)
~~~