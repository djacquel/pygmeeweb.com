---
layout: post
title: "Behavior Driven Development with Behat and Symfony"
comments: true
---

How to use Behat with Symfony

## Prerequisites

We'll be starting from a [fresh version of Symfony]({% post_url 2013-08-25-how-to-make-a-base-symfony-installation-on-ubuntu %}) but you can start from any Symfony config you want, just **RTFMB** [Read The F***** Doc for Behat] (http://behat.org/)

## Install

Add phpUnit, [Behat symfony extension](http://extensions.behat.org/symfony2/), [Mink extension](http://extensions.behat.org/mink/) and browserkit to your require-dev paragraph in your **composer.json** file.

{% highlight json %}
[...]
    "require": {
        [...]
    },
    "require-dev": {
        "phpunit/phpunit": "~3.7",
        "behat/symfony2-extension": "1.1.*@dev",
        "behat/mink-extension": "1.3.*@dev",
        "behat/mink-browserkit-driver": "1.1.*@dev"
    },
    "scripts": {

[...]
{% endhighlight %}

Run a
{% highlight bash %}
composer update
bin/behat --init
{% endhighlight %}

This is supposed to give you a nice
{% highlight bash %}
No scenarios
No steps
0m0.011s
{% endhighlight %}

You now have a full BDD tool up and running, now we'll configure it.

## Using Behat

### Configure Behat

Create a behat.yml file a you site root.

{% highlight yaml %}
default:
default:
    paths:
        features: features
        bootstrap: %behat.paths.features%/bootstrap

    extensions:
        Behat\Symfony2Extension\Extension:
            mink_driver: true

        Behat\MinkExtension\Extension:
            # this is the default homepage for tests
            base_url:  'http://page.dev/app_dev.php'
            default_session: 'symfony2'
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

See you in a next article on Behat soon. And once again : [read the doc] (http://behat.org/)


## Usefull commands

|Command    | Wat!  |
|------|------|
| bin/behat --init | create an empty *features* folder |
| bin/behat -h | Give useful help on available command line for Behat |
