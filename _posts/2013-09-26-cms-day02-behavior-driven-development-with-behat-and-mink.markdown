---
layout: post
title: "Symfony CMS - day 02 - Behavior Driven Development with Behat and Mink"
comments: true
---

How to do behavior Driven Development with Symfony

Second day of our [Build CMS Application with Symfony]({% post_url 2013-09-23-cms-day00 %}) articles suite.

Previous article [day 01 - Symfony installation]({% post_url 2013-09-25-cms-day01-symfony-installation %}).

## Usefull readings

[Behat documentation](http://behat.org/)

## Install

We'll install :

  - our BDD tools : [phpunit](https://github.com/sebastianbergmann/phpunit/), [Behat symfony extension](http://extensions.behat.org/symfony2/), [Mink extension](http://extensions.behat.org/mink/) and browserkit.
  - fixtures tools to be able to load test datas : [Doctrine fixtures Bundle](http://symfony.com/doc/current/bundles/DoctrineFixturesBundle/) and [Behat Fixtures extension](http://extensions.behat.org/doctrine-data-fixtures/)

In **composer.json** file add :
{% highlight json %}
[...]
    "require": {
        [...],
        "doctrine/data-fixtures": "1.0.*@dev",
        "doctrine/doctrine-fixtures-bundle": "dev-master"
    },
    "require-dev": {
        "phpunit/phpunit": "~3.7",
        "behat/symfony2-extension": "1.1.*@dev",
        "behat/mink-extension": "1.3.*@dev",
        "behat/mink-browserkit-driver": "1.1.*@dev",
        "vipsoft/doctrine-data-fixtures-extension": "*"
    },
    "scripts": {

[...]
{% endhighlight %}

Run a
{% highlight bash %}
composer update
{% endhighlight %}

You now have a full BDD tool up and running, now we'll configure it.

## Using Behat

### Creating a test bundle

In order to have all our tests at the same place, we create an empty bundle, with no route update.

{% highlight bash %}
app/console generate:bundle --namespace=My/TestBundle
{% endhighlight %}

Enable fixture and test Bundle in app/AppKernel.php
{% highlight php %}
class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = array(
          [...]
          new Doctrine\Bundle\FixturesBundle\DoctrineFixturesBundle(),
          new My\TestBundle\MyTestBundle(),
[...]
{% endhighlight %}

Create default test folder in our Test Bundle.
{% highlight bash %}
bin/behat --init "@MyTestBundle"
{% endhighlight %}
This creates a src/My/TestBundle/Features folder. Our tests lives here.

### Configure Behat

Create a behat.yml file a you site root.
{% highlight yaml %}
default:
    paths:
        features: src/My/TestBundle/Features/
        bootstrap: %behat.paths.features%/bootstrap
    context:
        class:  My\TestBundle\Features\Context\FeatureContext

    extensions:
        Behat\Symfony2Extension\Extension:
            mink_driver: true

        Behat\MinkExtension\Extension:
            base_url:  'http://page.dev/app_dev.php'
            default_session: 'symfony2'

        VIPSoft\DoctrineDataFixturesExtension\Extension:
          lifetime:    feature
          autoload:    true
          fixtures: ~
{% endhighlight %}

## Create your first feature test

Create a file named *features/test.feature*.

{% highlight yaml %}
Feature: Fresh Symfony installation
  In order to start developing a Symfony application
  As a developer
  I need to be able to see the default Symfony Standard Edition Home page

  Scenario: View the home page
    Given I am on the homepage
    Then I should see "Hello"
{% endhighlight %}

Run a
{% highlight bash %}
bin/behat --init
{% endhighlight %}

This is supposed to give you a nice
{% highlight bash %}
Feature: Fresh Symfony installation
  In order to start developing a Symfony application
  As a developer
  I need to be able to see the default Symfony Standard Edition Home page

  Scenario: View the home page # features/symfony-fresh-install.feature:6
    Given I am on the homepage # Behat\MinkExtension\Context\MinkContext::iAmOnHomepage()
    Then I should see "Hello"  # Behat\MinkExtension\Context\MinkContext::assertPageContainsText()
      The text "Hello" was not found anywhere in the text of the current page.

1 scenario (1 failed)
2 steps (1 passed, 1 failed)
0m1.155s
{% endhighlight %}

It work ! You can now start doing Behavior Driven Development on your brand new Symfony application.

Next article will be on [Sonata Admin Installation]({% post_url 2013-10-03-cms-day03-sonata-admin-installation %}).