---
layout: post
title: "install sketchup with wine on ubuntu 12.04"
published: true
comments: true
---

Installing sketchup on Ubuntu 12.04

## Check

Do we have graphic acceleration ? All lights must be green (YES)
{% highlight bash %}
/usr/lib/nux/unity_support_test -p
{% endhighlight %}

Then
{% highlight bash %}
sudo add-apt-repository ppa:ubuntu-wine/ppa
sudo apt-get update
sudo apt-get install wine1.7
sudo apt-get install winetricks
{% endhighlight %}sudo add-apt-repository ppa:ubuntu-wine/ppa

{% highlight bash %}
winecfg
{% endhighlight %}


http://www.sketchup.com/download/sketchup-make/windows/thank-you




-------------------- WIP

{% highlight bash %}
sudo add-apt-repository ppa:ubuntu-wine/ppa
sudo apt-get update
sudo apt-get install wine1.5
{% endhighlight %}