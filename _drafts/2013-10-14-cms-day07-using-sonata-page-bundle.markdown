---
layout: post
title: "Symfony CMS - day 07 - Using Sonata Page Bundle"
published: true
comments: true
---

Now that we have a working CMS engine, let's use it.

Seventh day of our [small CMS with Symfony howto]({% post_url 2013-09-23-cms-day00 %}). Previous article was about [Data Fixtures]({% post_url 2013-10-14-cms-day06-data-fixtures %}).

Now, our application is ready for use. Today we'll create some pages.

## Removing the AcmeDemoBundle

We first remove all references to AcmeDemoBundle.

from app/AppKernel.php
{% highlight php %}
    $bundles[] = new Acme\DemoBundle\AcmeDemoBundle();
{% endhighlight %}

from app/config/routing_dev.yml
{% highlight yaml %}
# AcmeDemoBundle routes (to be removed)
_acme_demo:
    resource: "@AcmeDemoBundle/Resources/config/routing.yml"
{% endhighlight %}

finally remove **src/Acme** folder.

## Writing tests

We want to create a home page with a Title and a text.

{% highlight bash %}
touch src/My/BDDBundle/Features/06-sonata-page-use.feature
{% endhighlight %}

{% highlight yaml %}
@homepage
Feature: Visiting Home page
  In order get wat! the site is about
  As a smart user
  I need to be able to see the some informations on the home page

  Scenario: View the home page
    Given I am on the homepage
    Then I should see "My Title" in the "h1.siteTitle" element
    And I should see "I love Espelette pepper !" in the "h2.subtitle" element
    And I should not see "yup!"
{% endhighlight %}

Run a : **bin/behat --tags="homepage"**

We have tests not passing ! That's good.

## Setup the default site

This, of course can be made through the admin interface.

A simple command can do this to :
{% highlight yaml %}
app/console sonata:page:create-site --enabled=true --name=MySite --locale=en --host=tuto.dev --relativePath=/ --enabledFrom=now --enabledTo="+10 years" --default=true
{% endhighlight %}

## Setup the default template

This cannot be made

Create a template page, that will declare default block for pages.

 - Go to [Page list](https://tuto.dev/app_dev.php/admin/sonata/page/page/list) and click on **Add new**.
 - Fill **name = global** and tick **enabled** then press **Create**
 - Click on **Block list** in the left menu, then "add new".
 - in the block types list choose **sonata.page.block.container**
 - name = **Header block**
 - options tab : thick **enabled**
 - layout : **{content}**
 - click **create**






Then run :
{% highlight yaml %}
app/console sonata:page:update-core-routes --site=all
app/console sonata:page:create-snapshots --site=all
{% endhighlight %}




{% highlight bash %}
app/console doctrine:database:drop --force
app/console doctrine:schema:create
chmod 664 app/data/database.sqlite
app/console doctrine:fixtures:load --fixtures=src/Application/Sonata/UserBundle/DataFixtures/ORM/ -n
app/console sonata:page:create-site --enabled=true --name=MySite --locale=en --host=tuto.dev --relativePath=/ --enabledFrom=now --enabledTo="+10 years" --default=true
{% endhighlight %}


We create the default template





app/console doctrine:database:drop --force
app/console doctrine:schema:create
chmod 664 app/data/database.sqlite
app/console doctrine:fixtures:load --fixtures=src/Application/Sonata/UserBundle/DataFixtures/ORM/ -n
app/console sonata:page:create-site --enabled=true --name=MySite --locale=en --host=tuto.dev --relativePath=/ --enabledFrom=now --enabledTo="+10 years" --default=true







Catchable Fatal Error: Argument 3 passed to Sonata\DoctrineORMAdminBundle\Block\AuditBlockService::__construct() must be an instance of SimpleThings\EntityAudit\AuditReader, null given, called in /home/djacquel/www/tuto.dev/app/cache/dev/appDevDebugProjectContainer.php on line 3652 and defined in /home/djacquel/www/tuto.dev/vendor/sonata-project/doctrine-orm-admin-bundle/Sonata/DoctrineORMAdminBundle/Block/AuditBlockService.php line 40

add "simplethings/entity-audit-bundle": "0.*@dev" to comoposer.json


kernel
new SimpleThings\EntityAudit\SimpleThingsEntityAuditBundle(),

sonata_block:
[...]
    context_manager: sonata.page.block.context_manager

https://github.com/sonata-project/SonataPageBundle/issues/186



Fixture
/home/djacquel/www/tuto.dev/src/Application/Sonata/PageBundle/DataFixtures/ORM/020-LoadPageData.php





