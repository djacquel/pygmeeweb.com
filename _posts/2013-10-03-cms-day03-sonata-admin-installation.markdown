---
layout: post
title: "Symfony CMS - day 03 - Sonata Admin Installation"
comments: true
---

Installing Sonata Admin Bundle.

Third day of our [Build CMS Application with Symfony]({% post_url 2013-09-23-cms-day00 %}) articles suite.

Previous article [Behavior Driven Development with Behat and Mink]({% post_url 2013-09-26-cms-day02-behavior-driven-development-with-behat-and-mink %}).

Symfony standard Edition is not enough to build a small Content Management System. In this articles suite, we will discover how to use some of the most powerful Sonata Bundles. Today : [Sonata Admin Bundle](http://sonata-project.org/bundles/admin/master/doc/index.html).

## Install

In your *composer.json* file, add :

{% highlight json %}
[...]
    "require": {
        [...],
        "sonata-project/admin-bundle": "dev-master",
        "sonata-project/intl-bundle": "2.2.*@dev",
        "sonata-project/doctrine-orm-admin-bundle": "2.2.*@dev",
        "sonata-project/block-bundle": "2.2.*@dev",
        "sonata-project/jquery-bundle": "1.8.*",
        "knplabs/knp-menu-bundle": "1.1.*@dev"
    },
[...]
{% endhighlight %}

Then run a
{% highlight bash %}
composer update
{% endhighlight %}

## Sonata Admin Bundle configuration

Enable our new bundles in the kernel :
{% highlight php %}
// app/AppKernel.php
public function registerBundles()
{
    return array(
        // ...

        // Add you dependencies
        new Sonata\BlockBundle\SonataBlockBundle(),
        new Sonata\jQueryBundle\SonatajQueryBundle(),
        new Knp\Bundle\MenuBundle\KnpMenuBundle(),
        //...

        // If you haven't already, add the storage bundle
        // This example uses SonataDoctrineORMAdmin but
        // it works the same with the alternatives
        new Sonata\DoctrineORMAdminBundle\SonataDoctrineORMAdminBundle(),

        // Then add SonataAdminBundle
        new Sonata\AdminBundle\SonataAdminBundle(),
        // ...
    );
}
{% endhighlight %}

## some cleanup

{% highlight bash %}
app/console assets:install web --symlink
app/console cache:clear
{% endhighlight %}

You can now point to http://page.dev/app_dev.php/admin/dashboard

## Writing tests

As we [have Behat installed]({% post_url 2013-09-26-cms-day02-behavior-driven-development-with-behat-and-mink %}), we can write tests for Sonata Admin install.

{% highlight yaml %}
Feature: sonata admin installation
  In order to manage my application's datas
  As a developer
  I need to be able to see the sonata admin

  Scenario: Sonata admin home  page
    Given I am on "/admin/dashboard"
    Then I should see "Sonata admin"
{% endhighlight %}

{% highlight yaml %}
bin/behat
{% endhighlight %}
And you must have all tests passing.

Next article will be on [Sonata User Installation]({% post_url 2013-10-04-cms-day04-sonata-user-installation %})