---
title: "Symfony CMS - day 01 - Symfony installation"
comments: true
---

A quick Symfony base installation.


First day of our [Build CMS Application with Symfony][cms0] articles suite.

## Pre-requisites

  - base knowledge of Symfony 2 ([see the doc][Symfony2Doc]),
  - composer installed globally (see [Composer doc][ComposerGlobal]),
  - a virtual host setup (see [Setup a virtual host on Ubuntu][vhostSetup]),
  - if you want to do some clever git version : [gitflow on Github][gitflow] installed

## Let's go for the base install

Verify the last version of Symfony on the [Download page][Symfony download]

Create a [tuto.dev virtual hosts][vhostSetup]

{% highlight bash %}
cd /home/www/tuto.dev

# here the last parameter "2.4.4" is the version previously checked
composer create-project symfony/framework-standard-edition . 2.4.4
{% endhighlight %}

During this install you will be prompted for some settings (database, etc ...). You can validate all the default, we'll take care of this later.

## Setting files permissions

I use this code to set permissions on my dev box. Other ways can be found in [Symfony install doc][Symfony file permissions].

{% highlight bash %}
cd /home/www/tuto.dev
rm -rf app/cache/*
rm -rf app/logs/*
mkdir app/data # will hosts our database
APACHEUSER=`ps aux | grep -E '[a]pache|[h]ttpd' | grep -v root | head -1 | cut -d\  -f1`
sudo setfacl -R -m u:$APACHEUSER:rwX -m u:`whoami`:rwX app/cache app/logs app/data
sudo setfacl -dR -m u:$APACHEUSER:rwX -m u:`whoami`:rwX app/cache app/logs app/data
{% endhighlight %}

You can now point to [http://tuto.dev/config.php/](http://tuto.dev/config.php/) to check our configuration.
Then make appropriates change to your **/etc/php5/apache2/php.ini** file.

**Note** :  On ubuntu 12.04 xdebug config is done in **/etc/php5/apache2/conf.d/20-xdebug.ini**.

{% highlight bash %}
sudo service apache2 restart
{% endhighlight %}

You also have to make the check on CLI side as the php.ini file used is different.

{% highlight bash %}
# can be useful
chmod 777 app/check.php
php app/check.php
{% endhighlight %}

Then make appropriates change to your /etc/php5/cli/php.ini file.

If you want to upgrade your php to a newest version you can read my
[Install PHP 5-5-4 on Ubuntu 12-04][ubuntuPHP544].

One more
{% highlight bash %}
sudo service apache2 restart
{% endhighlight %}
and your done. Nearly.

You can already see your page at [http://tuto.dev/app_dev.php](http://tuto.dev/app_dev.php).

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
    database_path: '%kernel.root_dir%/data/database.sqlite'
    database_memory: true
    mailer_transport: smtp
    mailer_encryption: ssl
    mailer_host: in.mailjet.com
    mailer_user: mailjetuser
    mailer_password: mailjetpassword
    locale: en
    secret: areallylongsecretkey
{% endhighlight %}

You also need to uncomment the **path** line in app/config/config.yml and to add the **memory** line.
{% highlight yaml %}
# app/config/config.yml
[...]
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

Create the database.
{% highlight bash %}
    app/console doctrine:database:create
    chmod 664 app/data/database.sqlite
{% endhighlight %}

Finally uncomment **translator** line :
{% highlight yaml %}
# app/config/config.yml
[...]
framework:
    #esi:             ~
    translator:      { fallback: %locale% }
{% endhighlight %}

## Hacking composer.json

Two little things about composer and __composer update__ :

  - Composer makes a hard copy of your assets (css, js, img) in the **web/ folder**. When you work on Linux you can just make **symlinks**. So we will add a **"symfony-assets-install": "symlink"** directive in composer.json.
  - Composer verifies that your parameters are conform to **config.yml.dist** template. So it deletes **path** and **memory** lines in our **parameter.yml**. We will add a **"keep-outdated": true** **incenteev-parameters** line in composer.json in order to keep our sqlite settings (path and memory).
  If you don't do this, **doctrine.dbal.path** and **doctrine.dbal.memory** are deleted from your parameter.yml file each time you do a **composer update**.

Add two lines in composer.json
{% highlight json %}
    [...]
    "extra": {
        "symfony-app-dir": "app",
        "symfony-web-dir": "web",
        "incenteev-parameters": {
            "file": "app/config/parameters.yml",
            "keep-outdated": true
        },
        "branch-alias": {
            "dev-master": "2.3-dev"
        },
        "symfony-assets-install": "symlink"
    }
    [...]
{% endhighlight %}

## Version the code

Yes we are supposed to work with versioning even when working alone.
I personally work with [git-flow][gitflow].

{% highlight bash %}
    git init
    git add .
    git commit -m "Symfony initial install"
{% endhighlight %}

Et voilà ! Next step will be to install [Behavior Driven Development tools][cms2]

{% include _links_library.markdown %}
