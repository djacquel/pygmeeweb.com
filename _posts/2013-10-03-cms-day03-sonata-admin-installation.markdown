---
layout: post
title: "Symfony CMS - day 03 - Sonata Admin Installation"
comments: true
---

Installing Sonata Admin Bundle.

Third day of our [Build CMS Application with Symfony]({% post_url 2013-09-23-cms-day00 %}) articles suite.

Previous article [Behavior Driven Development with Behat and Mink]({% post_url 2013-09-26-cms-day02-behavior-driven-development-with-behat-and-mink %}).

Symfony standard Edition is not enough to build a small Content Management System. In this articles suite, we will discover how to use some of the most powerful Sonata Bundles. Today : [Sonata Admin Bundle](http://sonata-project.org/bundles/admin/master/doc/index.html).

## Write the tests

As we [have Behat and Mink installed]({% post_url 2013-09-26-cms-day02-behavior-driven-development-with-behat-and-mink %}), we can write tests for Sonata Admin install.

{% highlight bash %}
touch src/My/BDDBundle/Features/02-sonata-admin-install.feature
{% endhighlight %}

{% highlight yaml %}
@sonataAdmin
Feature: sonata admin installation
  In order to manage my application's datas
  As a smart developer
  I need to be able to see the sonata admin home page

  Scenario: Go to Sonata admin home  page
    Given I am on "/admin/dashboard"
    Then I should see "Sonata admin"
{% endhighlight %}

The **@sonataAdmin** line is a tag. We can call bin/behat and just play feature or scenario associated with this tag. See [Gherkin tags documentation](http://docs.behat.org/guides/1.gherkin.html#tags) and [Gherkin Filters documentation](http://docs.behat.org/guides/6.cli.html#gherkin-filters).

{% highlight bash %}
# play all the features
bin/behat

# play only @sonataAdmin tagged features or scenarios
bin/behat --tags="@sonataAdmin"
{% endhighlight %}

Nevertheless, we get an error, as Sonata admin is not installed yet.

## Install Sonata Admin Bundle

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
        "sonata-project/cache-bundle": "2.1.*@dev",
        "knplabs/knp-menu-bundle": "1.1.*@dev"
    },
[...]
{% endhighlight %}

Then run a
{% highlight bash %}
composer update
{% endhighlight %}

## Sonata Admin Bundle configuration

### Sonata Block Bundle

{% highlight yaml %}
# app/config/config.yml
sonata_block:
    default_contexts: [cms]
    blocks:
        # Enable the SonataAdminBundle block
        sonata.admin.block.admin_list:
            contexts:   [admin]
{% endhighlight %}

### routing

{% highlight yaml %}
# app/config/routing.yml
admin:
    resource: '@SonataAdminBundle/Resources/config/routing/sonata_admin.xml'
    prefix: /admin

_sonata_admin:
    resource: .
    type: sonata_admin
    prefix: /admin
{% endhighlight %}

### Kernel

Enable our new bundles in the kernel :
{% highlight php %}
// app/AppKernel.php
public function registerBundles()
{
    return array(
        // ...

        // Sonata admin dependencies
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

## Publishing web assets + some cleanup

{% highlight bash %}
app/console assets:install web --symlink
app/console cache:clear
{% endhighlight %}

## Passing tests

{% highlight bash %}
bin/behat --tags="@sonataAdmin"
{% endhighlight %}

Greeeen !

Sonata Admin is now installed. Not usable to manage you application yet, but installed and working.

See you soon.

Next article will be on [Sonata User Installation]({% post_url 2013-10-04-cms-day04-sonata-user-installation %})