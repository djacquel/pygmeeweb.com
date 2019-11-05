---
title: "Install latest VirtualBox and Vagrant on Ubuntu 12.04"
description: ""
category:
tags: []
published: true
comments: true
---

{% include _links_library.markdown %}
I want to develop as close as it can be to my production machine configuration. [Vagrant][vagrant] to the rescue !
Let's install the latest version of VirtualBox and Vagrant on our good old Ubuntu 12.04 Precise Pangolin.

## VirtualBox

As the virtualbox version included in Ubuntu 12.04 is an old version, we will add the official VirtualBox repository.

## Remove old versions
First test if we already have virtualbox installed :

{% highlight bash %}
ps aux| grep virtualbox
{% endhighlight %}

THis can give you this kind of result :

{% highlight bash %}
Package: virtualbox-4.2
Status: install ok installed
...
{% endhighlight %}

Note the package name can be __virtualbox__ or something like __virtualbox-4.2__ :

{% highlight bash %}
sudo apt-get purge virtualbox-4.2
pkill virtualbox-4.2
{% endhighlight %}

Install virtualbox-4.3 and dkms through synaptic

[vagrant]: https://www.vagrantup.com/

Go to [Vagrant download page][http://www.vagrantup.com/downloads.html]

Download the deb file for your system and install it by clicking.

http://www.icchasethi.com/installing-latest-vagrant-and-virtualbox-version-on-ubuntu-12-01/
