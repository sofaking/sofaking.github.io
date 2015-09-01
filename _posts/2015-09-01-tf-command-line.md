---
layout: default
title: tf command line tips
tags: tfs team-explorer-everywhere bash
---

# {{ page.title }}
<ul class="tags">
  {% for tag in page.tags %}
  <li>{{ tag }}</li>
  {% endfor %}
</ul>

At my current job I have to deal with TFS and since I'm on Mac and prefer to work from terminal here are my notes.

#### Unmap all workspace mappings

{% highlight bash %}
tf workfold -workspace:workspace_name |
 grep Users |
 cut -d ' ' -f 2 |
 sed 's/.$//' |
 xargs -I FILE tf workfold -workspace:workspace_name -unmap FILE
{% endhighlight %}

`tf workfold` output usually consists of pairs of server/local folder mappings and some garbage  
`grep Users` cut all garbage off  
`cut -d ' ' -f 2` leaves only first element of mapping pair, which is server path  
`sed 's/.$//'` removes last symbol in path which is ':'  
then `xargs` passes each line of output as argument for `tf workfold`  
Simple, isn't it

#### Map folders as per workspace.txt

{% highlight bash %}
sed -e 's/://' -e 's/\\/\//g' -e 's/\$(SourceDir)/\/Users\/aup\/Documents\/workspace_name/' workspace.txt |
 tr -d '/015' |
 xargs -n 2 sh -c 'tf workfold -workspace:workspace_name -map $1 $2' argv0
{% endhighlight %}

sed part is quite straightforward, but the rest is rather tricky.  
`tr` removes carriage return symbols brought from Windows  
`-n 2`, `sh -c` and `argv0` are all necessary for `xargs` here, since only with this mix I was able to get `tf` to accept arguments. Plain execution of tf command (like in unmap example) doesn't work here  
`-n 2` splits imput in pairs of arguments  
`argv0` passes those arguments to `sh -c`, that in turn just executes given string as shell command  
BTW, very useful option for xargs debugging: `-t`
