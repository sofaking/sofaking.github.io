---
layout: default
title: Изменение локали в Андроид через adb
tags: adb locale android
---

# {{ page.title }}
<ul class="tags">
{% for tag in page.tags %}
<li>{{ tag }}</li>
{% endfor %}
</ul>

Столкнулся с данной проблемой снимая скриншоты приложения для разных локалей. В данном конкретном случае, скриншоты должны были пойти в локальные Google Play магазины.

{% highlight bash %}
adb shell 'setprop persist.sys.language en; setprop persist.sys.country US; setprop ctl.restart zygote'
{% endhighlight %}

Известные мне пары языков:
{% highlight ruby %}
[
  %w(en US), # American English
  %w(fr FR), # French
  %w(pt PT), # Portuguese
  %w(de DE), # Deutch
  %w(ru RU), # Russian
  %w(it IT), # Italian
  %w(es ES), # Spanish
  %w(es US), # American Spanish
  %w(pt BR), # Brazilian Portuguese
  %w(fi FI), # Finnish
  %w(da DK), # Danish
  %w(nb NO), # Norwegian
  %w(nl NL), # Dutch
  %w(pl PL), # Polish
  %w(sv SE), # Swedish
  %w(tr TR), # Turkish
  %w(zh CN), # Chinese
  %w(ko KR), # Korean
]
{% endhighlight %}
