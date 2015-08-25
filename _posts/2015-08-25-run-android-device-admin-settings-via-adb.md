---
layout: default
title: Run Android Device Administrator Settings via adb
tags: android adb appium
---

# {{ page.title }}
<ul class="tags">
  {% for tag in page.tags %}
  <li>{{ tag }}</li>
  {% endfor %}
</ul>

An application I'm testing at the moment requires device administrator permissions. So, before uninstalling the app I need to deactivate device administrator.
On Nexus 5 with Android 5.1 on board it could be done with a command below. Please note, with other devices/androids it could be invalid.
{% highlight bash %}
adb shell am start com.android.settings/.DeviceAdminSettings
{% endhighlight %}

### Bonus track

Ruby module I'm using to do that from tests:
{% highlight ruby %}
module Device
  def deactivate_device_administrator(app_name)
    @app_name = app_name
    open_device_admin_settings
    deactivate_administrator
  end

  private

  def open_device_admin_settings
    `adb shell am start com.android.settings/.DeviceAdminSettings`
  end

  def deactivate_administrator
    return unless administrator?

    find_element(name: @app_name).click
    find_element(id: 'com.android.settings:id/action_button').click
  end

  def administrator?
    app_index = find_elements(id: 'com.android.settings:id/name').index { |el| el.text == @app_name }
    app_checkbox = find_elements(id: 'com.android.settings:id/checkbox')[app_index]
    app_checkbox.attribute('checked') == 'true'
  end
end
{% endhighlight %}

And somewhere in step definitions:
{% highlight ruby %}
Given(/^I've installed the app$/) do
  start_driver
  deactivate_device_administrator 'Very Demanding App'
  restart
end
{% endhighlight %}
