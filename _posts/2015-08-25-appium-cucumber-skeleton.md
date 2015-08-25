---
layout: default
title: Skeleton for Appium-Cucumber tests
tags: ruby appium cucumber
---

# {{ page.title }}
<ul class="tags">
  {% for tag in page.tags %}
  <li>{{ tag }}</li>
  {% endfor %}
</ul>

After several projects using Cucumber and Appium/Calabash I end up with kind of golden standard for the skeleton of tests.
Nothing really special, but still it allows you to stop thinking about boilerplate and jump right into writing actual tests.
The tree looks like this:
{% highlight ruby %}
.
|__apk\
|__features\
| |__step_definitions\
| |__support\
| | |__app.rb
| | |__env.rb
|__Gemfile
|__Gemfile.lock
|__lib\
| |__page_object.rb
| |__page_objects\
{% endhighlight %}

And the contens of these files as follows:

{% highlight ruby %}
# features/support/env.rb
Bundler.require :default
  
require_relative 'app'
require_relative '../../lib/page_object'
  
CAPABILITIES = { caps: { deviceName: 'Nexus',
                         platformName: 'android',
                         app: File.join(Dir.pwd, 'apk', 'AnApp.apk'),
                         newCommandTimeout: 300
                       },
                 appium_lib: { wait: 1 } }

Appium::Driver.new(CAPABILITIES)

Appium.promote_appium_methods([App, PageObject])

World(App)
{% endhighlight %}
Notes:

* newCommandTimeout: 300 - allows you to take your time while debugging with pry. Default value is too short
* wait: 1 - reduced default timeout for find_element to 1 second. Usually mobile applications are quite snappy so that's enough.
 In complicated cases, like communication with server, I prefer explicit Spin Asserts, like `wait_true`

{% highlight ruby %}
# features/support/app.rb
module App
  def on(page_object_class)
    if block_given?
      yield page_object_class.new
    else
      page_object_class.new
    end
  end
end
{% endhighlight %}
Notes:

* this is shamelessly ripped from page_object gem. But basically, you can do in step files something like `on(LoginPage).login_with login, password`

{% highlight ruby %}
# lib/page_object.rb
module PageObject
end

Pathname('lib/page_objects').each_child { |file| require file.expand_path }
{% endhighlight %}
Notes:

* PageObject has to be included in every page object (located in `lib/page_objects`)
* `Pathname('lib/page_objects')...` automatically requires every page object file. Has to be slightly improved in case page_objets has subdirectories.
