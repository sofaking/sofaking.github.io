---
layout: default
title: Override Selenium's Element#click with SimpleDelegator
tags: ruby selenium
---

# {{ page.title }}
<ul class="tags">
  {% for tag in page.tags %}
  <li>{{ tag }}</li>
  {% endfor %}
</ul>

Sometimes you click on an element that has just reloaded. Then Selenium returns `Element 1 could not be tapped`.
To overcome that you can use something like:
{% highlight ruby %}
def compose
  start = Time.now
  begin
    find_element(name: 'Compose').click
  rescue Selenium::WebDriver::Error::UnknownError => e
    fail "Compose button hasn't been tapped in 5 seconds" if Time.now - start > 5
    retry if e.message.include?('could not be tapped')
    raise e
  end
end
{% endhighlight %}

However if there are several such 'unstable' components, you can decorate #click as follows:
{% highlight ruby %}
def compose_button
  FlickeringButton.new(find_element(name: 'Compose')).click
end

def FlickeringButton < SimpleDelegator
  def click
    start = Time.now
    begin
      super
    rescue Selenium::WebDriver::Error::UnknownError => e
      fail "Button hasn't been tapped in 5 seconds" if Time.now - start > 5
      retry if e.message.include?('could not be tapped')
      raise e
    end
  end
end
{% endhighlight %}

One little quirk I noticed. `super` inside subclass of SimpleDelegator doesn't work during Pry session. Use `self.__getobj__.name_of_method` instead.
