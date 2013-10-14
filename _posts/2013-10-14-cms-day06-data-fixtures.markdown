---
layout: post
title: "Symfony CMS - day 06 - Data Fixtures"

published: true
comments: true
---

How to inject default datas in my application : with Data Fixtures.


Sixth day of our [small CMS with Symfony howto]({% post_url 2013-09-23-cms-day00 %}). Previous article [Sonata Page Installation]({% post_url 2013-10-13-cms-day05-sonata-page-installation %}).

In a previous [article on BDD]({% post_url 2013-09-26-cms-day02-behavior-driven-development-with-behat-and-mink %}), we've installed some bundles that we are not using yet [DoctrineFixturesBundle](http://symfony.com/doc/current/bundles/DoctrineFixturesBundle/index.html)
and
[Doctrine Data Fixtures Extension for Behat](https://github.com/vipsoft/DoctrineDataFixturesExtension) (in composer.json : **doctrine/data-fixtures**, **doctrine/doctrine-fixtures-bundle** and **vipsoft/doctrine-data-fixtures-extension**).

Those bundles will help us inject default data in our application before tests or before another project phase (staging, prod, etc).

## User fixtures

The only user we've created was created with the FOS User commend :

{% highlight bash %}
app/console fos:user:create admin admin@example.com admin --super-admin
{% endhighlight %}

We can automate this task.

Create a file **src/Application/Sonata/UserBundle/DataFixtures/ORM/010-LoadUserData.php**

{% highlight php %}
<?php
// src/Application/Sonata/UserBundle/DataFixtures/ORM/010-LoadUserData.php
namespace Application\Sonata\UserBundle\DataFixtures\ORM;

use Doctrine\Common\DataFixtures\FixtureInterface;
use Doctrine\Common\DataFixtures\AbstractFixture;
use Doctrine\Common\DataFixtures\OrderedFixtureInterface;
use Doctrine\Common\Persistence\ObjectManager;
use Symfony\Component\DependencyInjection\ContainerAwareInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;
use Symfony\Component\Yaml\Yaml;

use Application\Sonata\UserBundle\Entity\User;

class LoadUserData extends AbstractFixture
                   implements OrderedFixtureInterface,
                              FixtureInterface,
                              ContainerAwareInterface
{
    /**
     * @var ContainerInterface
     */
    private $container;

    /**
     * {@inheritDoc}
     */
    public function setContainer(ContainerInterface $container = null)
    {
        $this->container = $container;
    }

    /**
     * {@inheritDoc}
     */
    public function load(ObjectManager $manager)
    {

        $filename = __DIR__ . DIRECTORY_SEPARATOR  . '010-LoadUserData.yml';
        $yml      = Yaml::parse(file_get_contents($filename));
        $userManager = $this->container->get('fos_user.user_manager');

        foreach ($yml as $userReference => $data) {
            $user = $userManager->createUser();
            $user->setUsername($data['username']);
            $user->setEmail($data['email']);
            $user->setPlainPassword($data['plainPassword']);

            $user->setDateOfBirth(new \DateTime(strtotime($data['date_of_birth'])));
            $user->setFirstname($data['firstname']);
            $user->setLastname($data['lastname']);
            $user->setGender($data['gender']);
            $user->setPhone($data['phone']);
            $user->setEnabled(true);

            if(isset($data['isAdmin']) && $data['isAdmin']){
                $user -> setRoles(array('ROLE_SUPER_ADMIN'));
            }

            $userManager->updateUser($user, true);
            $this->addReference($userReference, $user);
        }
    }

    /**
     * {@inheritDoc}
     */
    public function getOrder()
    {
        return 1; // the order in which fixtures will be loaded
    }

}
?>
{% endhighlight %}

This file will load datas from **src/Application/Sonata/UserBundle/DataFixtures/ORM/010-LoadUserData.yml**

{% highlight yaml %}
# src/Application/Sonata/UserBundle/DataFixtures/ORM/010-LoadUserData.yml
useradmin:
    username: admin
    firstname: David
    lastname: Jacquel
    date_of_birth: 1980-12-24
    gender: m
    phone: +99999999999
    email: admin@example.com
    plainPassword: admin
    isAdmin: true

useralice:
    username: alice
    firstname: Alice
    lastname: Bonnon
    date_of_birth: 1968-12-27
    gender: f
    phone: +33675982044
    email: alice@example.com
    plainPassword: alice

userbob:
    username: bob
    firstname: Bob
    lastname: Leponge
    date_of_birth: 1976-10-24
    gender: m
    phone: +33898982044
    email: bob@example.com
    plainPassword: bob
{% endhighlight %}

Now if you run a :
{% highlight bash %}
app/console doctrine:fixtures:load
{% endhighlight %}

You will have database populated with three users. This will be useful to populate our database with known datas each time we run a test.

To make that our fixtures are loaded for each Feature, we'll uncomment the four last lines in our behat.yml file:

{% highlight yaml %}
# behat.yml
default:
    paths:
        features: src/My/BDDBundle/Features/
        bootstrap: %behat.paths.features%/bootstrap
    context:
        class:  My\BDDBundle\Features\Context\FeatureContext

    extensions:
        Behat\Symfony2Extension\Extension:
            mink_driver: true

        Behat\MinkExtension\Extension:
            base_url:  'http://tuto.dev/app_dev.php'
            default_session: 'symfony2'

        VIPSoft\DoctrineDataFixturesExtension\Extension:
          lifetime: feature
          autoload: true
          fixtures: ~
{% endhighlight %}

To verify this, we just rebuild an empty database :

{% highlight bash %}
app/console doctrine:database:drop --force
app/console doctrine:schema:create
chmod 664 app/data/database.sqlite
{% endhighlight %}

And run our tests.

{% highlight bash %}
bin/behat
{% endhighlight %}

All test are green.

## More tests

We can for example test if a normal user can access our admin.
It is supposed to get an "access denied" response.

{% highlight bash %}
touch src/My/BDDBundle/Features/05-data-fixtures.feature
{% endhighlight %}

{% highlight yaml %}
@fixtures
Feature: admin dashboard protection
  In order to protect admin
  As a smart developer
  I need to force forbid access to admin from basic authenticated user

  Scenario: Try to log to admin with basic user credentials
    Given I am logged in as Bob
    And I go to "/admin/dashboard"
    Then I should see "Access Denied"
    Then the response status code should be 403
{% endhighlight %}

Run :
{% highlight bash %}
bin/behat --tags="fixtures"
{% endhighlight %}

If you implement **iAmLoggedInAsBob** step in your context (see previous article). All the lights a greens.

See you next time for an article on how to use Sonata page.