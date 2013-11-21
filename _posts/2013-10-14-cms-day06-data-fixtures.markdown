---
layout: post
title: "Symfony CMS - day 06 - Data Fixtures"

published: true
comments: true
---

How to inject default datas in my application : with Data Fixtures.


Sixth day of our [small CMS with Symfony howto][cms0]. Previous article [Sonata Page Installation][cms5].

In a previous [article on BDD][cms2], we've installed some bundles that we are not using yet [DoctrineFixturesBundle][Doctrine fixtures Bundle]
and
[Doctrine Data Fixtures Extension for Behat][Behat Fixtures extension] (in composer.json : **doctrine/data-fixtures**, **doctrine/doctrine-fixtures-bundle** and **vipsoft/doctrine-data-fixtures-extension**).

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
{% endhighlight %}

This file will load datas from **src/Application/Sonata/UserBundle/DataFixtures/ORM/010-LoadUserData.yml**

{% highlight yaml %}
# src/Application/Sonata/UserBundle/DataFixtures/ORM/010-LoadUserData.yml
useradmin:
    username: admin
    firstname: David
    lastname: Jacquel
    date_of_birth: 1980-10-20
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
# to run all our fixtures all over our app
app/console doctrine:fixtures:load

# or just a specific fixture
app/console doctrine:fixtures:load --fixtures=src/Application/Sonata/UserBundle/DataFixtures/ORM/

# multiple fixtures
php app/console doctrine:fixtures:load --fixtures=/path/to/fixture1 --fixtures=/path/to/fixture2 --append
{% endhighlight %}

This will be useful to populate our database with known datas each time we run a test.

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
  I need to forbid access to admin from basic user

  Scenario: Try to access admin with basic user credentials
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

See you next time for an article on how to use sonata.

**Edit oct 22** : Implementation for the **iAmLoggedInAsBob** step. Note that we refactor ** iAmLoggedInAsAdmin** step.

{% highlight php %}
<?php
// src/My/BDDBundle/Features/Context/FeatureContext.php
class FeatureContext extends MinkContext
                     implements KernelAwareInterface
{
[...]
    /**
     * @Given /^I am logged in as admin$/
     */
    public function iAmLoggedInAsAdmin(){
        $this->loginAs('admin', 'admin');
    }

    /**
     * @Given /^I am logged in as Bob$/
     */
    public function iAmLoggedInAsBob(){
        $this->loginAs('bob', 'bob');
    }

    private function loginAs($user, $password){
        $this->visit('/admin/login');
        $this->fillField('username', $user);
        $this->fillField('password', $password);
        $this->pressButton('_submit');
    }

{% endhighlight %}