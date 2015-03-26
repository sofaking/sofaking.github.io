---
layout: post
title: Override Selenium Element#click with SimpleDelegator
tags: ruby selenium
---

Sometimes you click on an element that has just reloaded. Then Selenium returns `Element 1 could not be tapped`.
To overcome that You can use something like:
{% highlight ruby %}
 def compose
    start = Time.now
    begin
      compose_button.click
    rescue Selenium::WebDriver::Error::UnknownError => e
      fail "Compose button hasn't been tapped in #{DEFAULT_TIMEOUT} seconds" if Time.now - start > DEFAULT_TIMEOUT
      retry if e.message.include?('could not be tapped')
      raise e
    end
  end
{% endhighlight %}
