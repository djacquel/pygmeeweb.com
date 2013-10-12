---
layout: post
title: "Setup a virtual host on Ubuntu"
comments: true
---

Basic commands to setup a virtual host on Ubuntu 12.04

This is a small howto to setup a virtual host on Ubuntu 12.04 with an upgraded version of php and apache (see my article about [PHP and Apache upgrade on Ubuntu 12-04]({% post_url 2013-08-29-install-php-5-4-4-on-ubuntu-12-04 %}).
Default installation path ( **/home/www/tuto.dev** ) and host name ( **tuto.dev** ) will be used in all our tutorials.

{% highlight bash %}
sudo gedit /etc/hosts
{% endhighlight %}

add
{% highlight bash %}
127.0.0.1 tuto.dev
{% endhighlight %}

{% highlight bash %}
sudo touch /etc/apache2/sites-available/page.conf
sudo gedit /etc/apache2/sites-available/page.conf
{% endhighlight %}

{% highlight bash %}
# /etc/apache2/sites-available/page.conf
<VirtualHost *:80>
    ServerName tuto.dev

    DocumentRoot /home/www/tuto.dev/web

    <Directory /home/www/tuto.dev/web>
        Options Indexes FollowSymLinks
        AllowOverride All
        Order allow,deny
        allow from all
        # New directive needed in Apache 2.4.3:
        Require all granted
    </Directory>

    # logs are outside of root
    # this allows to make automatic build
    # and then allow git repo creation

    ErrorLog /home/logs/tuto.dev-error.log

    # Possible values include: debug, info, notice, warn, error, crit,
    # alert, emerg.

    LogLevel info

    CustomLog /home/logs/tuto.dev-access.log combined
</VirtualHost>
{% endhighlight %}

Enable your virtual hosts

{% highlight bash %}
sudo a2ensite page.conf
sudo service apache2 reload
{% endhighlight %}

You can then got to [http://tuto.dev][tuto.dev]


If you get the following error :
{% highlight bash %}
apache2: Could not reliably determine the server s
fully qualified domain name, using 127.0.1.1 for ServerName
 ... waiting apache2: Could not reliably determine
 the server s fully qualified domain name, using
 127.0.1.1 for ServerName
{% endhighlight %}


you can do a
{% highlight bash %}
sudo gedit /etc/apache2/httpd.conf
{% endhighlight %}

and add
{% highlight bash %}
ServerName localhost
{% endhighlight %}

A new
{% highlight bash %}
sudo service apache2 restart
{% endhighlight %}
a the error must be gone.

[tuto.dev]: http://tuto.dev