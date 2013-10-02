---
layout: post
title: "Setup a virtual host on Ubuntu"
comments: true
---

Basic commands to setup a virtual host on Ubuntu Linux

{% highlight bash %}
sudo gedit /etc/hosts
{% endhighlight %}

add
{% highlight bash %}
127.0.0.1 page.dev
{% endhighlight %}

{% highlight bash %}
sudo touch /etc/apache2/sites-available/page.conf
sudo gedit /etc/apache2/sites-available/page.conf
{% endhighlight %}

{% highlight bash %}
# /etc/apache2/sites-available/page.conf
<VirtualHost *:80>
    ServerName page.dev

    DocumentRoot /home/www/page.dev

    <Directory /home/www/page.dev>
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

    ErrorLog /home/logs/page.dev-error.log

    # Possible values include: debug, info, notice, warn, error, crit,
    # alert, emerg.

    LogLevel info

    CustomLog /home/logs/page.dev-access.log combined
</VirtualHost>
{% endhighlight %}

Enable your virtual hosts

{% highlight bash %}
sudo a2ensite page.conf
sudo service apache2 reload
{% endhighlight %}

You can then got to [http://page.dev][page.dev]


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

[page.dev]: http://page.dev