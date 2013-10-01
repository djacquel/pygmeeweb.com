---
layout: post
title: "Install PHP 5-5-4 on Ubuntu 12-04"
description: ""
category:
tags: []
published: true
---

Need a newest version of PHP on my Ubuntu 12.04 box.

Ondřej Surý and some fair guys from debian php team are maintaining ready made package for Ubuntu (see [Launchpad][Ondrej launchpad]).

## Installation

Save your php.ini file

{% highlight bash %}
sudo add-apt-repository ppa:ondrej/php5
sudo apt-get update
sudo apt-get dist-upgrade
{% endhighlight %}

If your virtual hosts file have an other extension than .conf, rename them, re a2ensite them and reload apache.

## All my virtual hosts are now 403 Forbidden !

You've just upgraded for an Apache 2.4.6+ and if you set your LogLevel to debug, you will find something like this in your error log :

{% highlight bash %}
[authz_core:error] [pid 31234] [client 127.0.0.1:49381] AH01630: client denied by server configuration: /home/www/site.dev
{% endhighlight %}

Yes ! A new security feature that appeared in Apache 2.4.3 and is set to __Require all denied__ by default. See[Apache Module mod_authz_host documentation](http://httpd.apache.org/docs/2.4/mod/mod_authz_host.html)
To unlock your virtual hosts, just add a __Require all granted__ in your virtual host configuration.

{% highlight bash %}
<Directory /home/www/site.dev>
   Order allow,deny
   Allow from all
   # New directive needed in Apache 2.4.3:
   Require all granted
</Directory>
{% endhighlight %}

I'm now running Apache 2.4.6 with php 5.5.4.

[Ondrej launchpad]: https://launchpad.net/~ondrej/+archive/php5