---
title: "Symfony CMS - day 04 - Sonata User Installation"
comments: true
---

How to install Sonata User Bundle.


Fourth day of our [Build CMS Application with Symfony]({% post_url 2013-09-23-cms-day00 %}) articles suite.

Previous article [Sonata Admin Installation]({% post_url 2013-10-03-cms-day03-sonata-admin-installation %}).

In order to manage user and efficiently secure our application, we will use [FOS User Bundle](https://github.com/FriendsOfSymfony/FOSUserBundle) and [Sonata User Bundle](http://www.sonata-project.org/bundles/user/master/doc/index.html).

## Writing tests

As we [have Behat installed]({% post_url 2013-09-26-cms-day02-behavior-driven-development-with-behat-and-mink %}), we can write tests for Sonata User install.

{% highlight bash %}
touch src/My/BDDBundle/Features/03-sonata-user-install.feature
{% endhighlight %}

{% highlight yaml %}
@sonataUser
Feature: sonata user installation
  In order to protect admin
  As a smart developer
  I need to force users to login before seeing admin

  Scenario: Forcing login before seeing admin page
    Given I am not logged
    And I go to "/admin/dashboard"
    Then the response status code should be 200
    And the url should match "/login"
{% endhighlight %}

The result of a **bin/behat --tags="sonataUser"** command will be :

{% highlight bash %}
[...]
You can implement step definitions for undefined steps with these snippets:

    /**
     * @Given /^I am not logged$/
     */
    public function iAmNotLogged()
    {
        throw new PendingException();
    }
{% endhighlight %}

Let's implement our first step in **src/My/BDDBundle/Features/Context/FeatureContext.php**.

{% highlight php %}
// src/My/BDDBundle/Features/Context/FeatureContext.php
    /**
     * @Given /^I am not logged$/
     */
    public function iAmNotLogged()
    {
        $this->visit('/logout');
    }
{% endhighlight %}

ReRun a :

{% highlight bash %}
bin/behat --tags="sonataUser"
{% endhighlight %}

Cool ! Behat understand our feature ! But the test fails ! That's good.

## Install Sonata User Bundle

In your *composer.json* file, add :

{% highlight json %}
[...]
    "require": {
        [...],
        "sonata-project/easy-extends-bundle": "2.1.*@dev",
        "sonata-project/user-bundle": "2.2.*@dev"
    },
[...]
{% endhighlight %}

Then run a
{% highlight bash %}
composer update
{% endhighlight %}

## Sonata User Bundle configuration

Enable our new bundles in the kernel :
{% highlight php %}
// app/AppKernel.php
public function registerBundles()
{
    return array(
        // ...
        new Sonata\EasyExtendsBundle\SonataEasyExtendsBundle(),
        new FOS\UserBundle\FOSUserBundle(),
        // extend the ``FOSUserBundle``
        new Sonata\UserBundle\SonataUserBundle('FOSUserBundle'),
    );
}
{% endhighlight %}

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
        types:
            json: Sonata\Doctrine\Types\JsonType

    orm:
        auto_generate_proxy_classes: %kernel.debug%

        # see https://github.com/stof/StofDoctrineExtensionsBundle/issues/141#issuecomment-7004310
        # auto_mapping: true        ----> MOVED |
                                                |
        entity_managers:                        |
            default:                            |
                                                |
                auto_mapping: true       <--- HERE !

                mappings:
                    SonataUserBundle: ~
                    FOSUserBundle: ~

[...]
# at the end of your file
sonata_user:
    security_acl: true
    manager_type: orm # can be orm or mongodb

fos_user:
    db_driver:      orm # can be orm or odm
    firewall_name:  main
    user_class:     Application\Sonata\UserBundle\Entity\User

    group:
        group_class: Application\Sonata\UserBundle\Entity\Group

{% endhighlight %}

Replace your **app/config/security.yml** content by:

{% highlight yaml %}
# app/config/security.yml
security:
    encoders:
        FOS\UserBundle\Model\UserInterface: sha512

    role_hierarchy:
        ROLE_ADMIN:       [ROLE_USER, ROLE_SONATA_ADMIN]
        ROLE_SUPER_ADMIN: [ROLE_ADMIN, ROLE_ALLOWED_TO_SWITCH]
        SONATA:
           # - ROLE_SONATA_PAGE_ADMIN_PAGE_EDIT  # if you are using acl then this line must be commented

    providers:
        fos_userbundle:
            id: fos_user.user_manager

    firewalls:
        # Disabling the security for the web debug toolbar, the profiler and Assetic.
        dev:
            pattern:  ^/(_(profiler|wdt)|css|images|js)/
            security: false

        # -> custom firewall for the admin area of the URL
        admin:
            pattern:            /admin(.*)
            context:            user
            form_login:
                provider:       fos_userbundle
                login_path:     /admin/login
                use_forward:    false
                check_path:     /admin/login_check
                failure_path:   null
            logout:
                path:           /admin/logout
            anonymous:          true
        # -> end custom configuration

        # defaut login area for standard users
        # This firewall is used to handle the public login area
        # This part is handled by the FOS User Bundle
        main:
            pattern:             .*
            context:             user
            form_login:
                provider:       fos_userbundle
                login_path:     /login
                use_forward:    false
                check_path:     /login_check
                failure_path:   null
            logout:             true
            anonymous:          true

    access_control:
        # URL of FOSUserBundle which need to be available to anonymous users
        - { path: ^/login$, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/register, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/resetting, role: IS_AUTHENTICATED_ANONYMOUSLY }

        # Admin login page needs to be access without credential
        - { path: ^/admin/login$, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/admin/logout$, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/admin/login_check$, role: IS_AUTHENTICATED_ANONYMOUSLY }

        # Secured part of the site
        # This config requires being logged for the whole site and having the admin role for the admin part.
        # Change these rules to adapt them to your needs
        - { path: ^/admin/, role: [ROLE_ADMIN, ROLE_SONATA_ADMIN] }
        - { path: ^/.*, role: IS_AUTHENTICATED_ANONYMOUSLY }

    acl:
        connection: default
{% endhighlight %}

{% highlight yaml %}
# app/config/routing.yml

# Sonata Admin Bundle
admin:
    resource: '@SonataAdminBundle/Resources/config/routing/sonata_admin.xml'
    prefix: /admin

_sonata_admin:
    resource: .
    type: sonata_admin
    prefix: /admin

# Sonata User Bundle
fos_user_security:
    resource: "@FOSUserBundle/Resources/config/routing/security.xml"

fos_user_profile:
    resource: "@SonataUserBundle/Resources/config/routing/profile.xml"
    prefix: /profile

# see https://github.com/sonata-project/SonataUserBundle/issues/105#issuecomment-7444424
# resolve bug #1
fos_user_profile_show:
    pattern:  /profile
    defaults: { _controller: SonataUserBundle:Profile:show }
    requirements:
        _method: GET

fos_user_register:
    resource: "@FOSUserBundle/Resources/config/routing/registration.xml"
    prefix: /register

fos_user_resetting:
    resource: "@FOSUserBundle/Resources/config/routing/resetting.xml"
    prefix: /resetting

fos_user_change_password:
    resource: "@FOSUserBundle/Resources/config/routing/change_password.xml"
    prefix: /profile

fos_user_group:
    resource: "@FOSUserBundle/Resources/config/routing/group.xml"
    prefix: /group

sonata_user:
    resource: '@SonataUserBundle/Resources/config/routing/admin_security.xml'
    prefix: /admin
{% endhighlight %}

We'll now generate the SonataUserBundle extension, in order to be able to extend our
**user and group models** in a bundle that extends SonataUserBundle, which itself extends FOSUSerBundle. We'll see that later in an other article.
{% highlight bash %}
app/console sonata:easy-extends:generate SonataUserBundle --dest="./src"
{% endhighlight %}

Then you can add the **ApplicationSonataUserBundle** mapping :
{% highlight yaml %}
# app/config/config.yml
[...]
doctrine:
    orm:
        entity_managers:
            default:
                mappings:
                    ApplicationSonataUserBundle: ~
                    SonataUserBundle: ~
                    FOSUserBundle: ~
[...]
{% endhighlight %}

and enable ApplicationSonataUserBundle in the kernel
{% highlight php %}
// app/AppKernel.php
public function registerBundles()
{
    return array(
        // ...
        new Application\Sonata\UserBundle\ApplicationSonataUserBundle(),
    );
}
{% endhighlight %}

You can now point to http://tuto.dev/app_dev.php/admin/dashboard and you're redirected to the login page if you were not previously logged.

## Passing tests

ReReRun a :

{% highlight bash %}
bin/behat --tags="sonataUser"
{% endhighlight %}

Green green green ! Go get a beer or a tea, and relax, Sonata User Bundle is correctly installed.
Of course, you are supposed to write some more tests to have a good coverage for functionalities.

For example to verify if a given user can login properly.

Create an admin user

{% highlight bash %}
# create database tables
app/console doctrine:schema:create
# create a super admin
app/console fos:user:create admin admin@example.com admin --super-admin
{% endhighlight %}

Write a new scenario in **src/My/BDDBundle/Features/03-sonata-user-install.feature**

{% highlight yaml %}
[...]
  Scenario: Login as admin
    Given I am not logged
    And I go to "/admin/dashboard"
    And I fill in "username" with "admin"
    And I fill in "password" with "admin"
    And I press "_submit"
    Then I should see "Users"
    And I should see "Logout"
{% endhighlight %}

A new **bin/behat --tags="sonataUser"** will give you an *All green* result.

Next time, we'll seen [Sonata Page Installation]({% post_url 2013-10-13-cms-day05-sonata-page-installation %})

See you soon !
