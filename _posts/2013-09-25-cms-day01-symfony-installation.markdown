---
layout: post
title: "Symfony CMS - day 01 - Symfony installation"
comments: true
---

A quick Symfony base installation.


First day of our [Build CMS Application with Symfony]({% post_url 2013-09-23-cms-day00 %}) articles suite.

## Pre-requisites

  - base knowledge of Symfony 2 ([see the doc][Symfony2 doc]),
  - composer installed globally (see [Composer doc][composer install globally]),
  - a virtual host setup (see [Setup a virtual host on Ubuntu]({% post_url 2013-08-24-setup-a-virtual-host-on-ubuntu %})). NB: the host must point to the /web folder of your Symfony install,
  - if you want to do some clever git version : [gitflow](https://github.com/nvie/gitflow "gitflow repository on github") installed

## Let's go for the base install

Verify the last version of Symfony on the [Download page][Symfony download]

Create a [page.dev virtual hosts]({% post_url 2013-08-24-setup-a-virtual-host-on-ubuntu %})

{% highlight bash %}
cd /home/www/page.dev

# here the last parameter "2.3.5" is the version previously checked
composer create-project symfony/framework-standard-edition . 2.3.5
{% endhighlight %}

During this install you will be prompted for some settings (database, etc ...). You can validate all the default, we'll take care of this later.

## Setting files permissions

I use this code to set permissions on my dev box. Other ways can be found in [Symfony install doc][file perms].

{% highlight bash %}
cd /home/www/page.dev
rm -rf app/cache/*
rm -rf app/logs/*
mkdir app/data # will hosts our database
APACHEUSER=`ps aux | grep -E '[a]pache|[h]ttpd' | grep -v root | head -1 | cut -d\  -f1`
sudo setfacl -R -m u:$APACHEUSER:rwX -m u:`whoami`:rwX app/cache app/logs app/data
sudo setfacl -dR -m u:$APACHEUSER:rwX -m u:`whoami`:rwX app/cache app/logs app/data
{% endhighlight %}

You can now point to [http://page.dev/config.php/](http://page.dev/config.php/) to check our configuration.
Then make appropriates change to your /etc/php5/apache2/php.ini file.
You also have to make the check on CLI side as the php.ini file used is different.

{% highlight bash %}
app/check.php
{% endhighlight %}
Then make appropriates change to your /etc/php5/cli/php.ini file.

If you want to upgrade your php to a newest version you can read my
[Install PHP 5-5-4 on Ubuntu 12-04]({% post_url 2013-08-29-install-php-5-4-4-on-ubuntu-12-04 %}).

One more
{% highlight bash %}
sudo service apache2 restart
{% endhighlight %}
and your done. Nearly.

You can already see your page at [http://page.dev/app_dev.php](http://page.dev/app_dev.php).

## Some more setup

### Database, mail and various parameters

For performance reasons during tests, we want to use Sqlite database.
This will allow us to use in memory db and to copy paste a fresh db between each test suite.

Setup is the following :

{% highlight yaml %}
# app/config/parameters.yml
# This file is auto-generated during the composer install
parameters:
    database_driver: pdo_sqlite
    database_host: null
    database_port: null
    database_name: null
    database_user: null
    database_password: null
    database_path: '%kernel.root_dir%/data/fnmns.sqlite'
    database_memory: true
    mailer_transport: smtp
    mailer_encryption: ssl
    mailer_host: in.mailjet.com
    mailer_user: mailjetuser
    mailer_password: mailjetpassword
    locale: fr
    secret: areallylongsecretkey
# end of file

# You also need to uncomment the "path" line in app/config/config.yml
# and to add the 'memory' line
doctrine:
    dbal:
        driver:   %database_driver%
        host:     %database_host%
        port:     %database_port%
        dbname:   %database_name%
        user:     %database_user%
        password: %database_password%
        charset:  UTF8
        path:     %database_path%
        memory:   %database_memory%
{% endhighlight %}

{% highlight bash %}
app/console doctrine:database:create
{% endhighlight %}

Create the app/data folder for your sqlite db, set the right permissions and create your database.
{% highlight bash %}
app/console doctrine:database:create
app/console doctrine:schema:update --force
{% endhighlight %}

## Hacking composer.json

Two little things about composer and __composer update__ :

  - Composer makes a hard copy of your assets (css, js, img) in the **web/ folder**. When you work on Linux you can just make **symlinks**. So we will add a **"symfony-assets-install": "symlink"** directive in composer.json.
  - Composer verifies that your parameters are conform to **config.yml.dist** template. So it deletes **path** and **memory** lines in our **parameter.yml**. We will add a **"keep-outdated": true** line in composer.json.

Add two lines in composer.json
{% highlight json %}
    [...]
    "extra": {
        "symfony-app-dir": "app",
        "symfony-web-dir": "web",
        "incenteev-parameters": {
            "file": "app/config/parameters.yml",
            "keep-outdated": true  <----------
        },
        "branch-alias": {
            "dev-master": "2.3-dev"
        },
        "symfony-assets-install": "symlink"  <----------
    }
    [...]
{% endhighlight %}

## Version the code

Yes we are supposed to work with versioning even when working alone.
I personally work with [git-flow](https://github.com/nvie/gitflow) but it's another story.

{% highlight bash %}
git init
git add .
git commit -m "Symfony initial install"
{% endhighlight %}

Et voilà ! Next step will be to install [Behavior Driven Development tools]({% post_url 2013-09-26-cms-day02-behavior-driven-development-with-behat-and-mink %})


[Symfony2 doc]: http://symfony.com/doc/current/index.html
[composer install globally]: http://getcomposer.org/doc/00-intro.md#globally
[Symfony download]: http://symfony.com/download
[file perms]: http://symfony.com/doc/current/book/installation.html#configuration-and-setup