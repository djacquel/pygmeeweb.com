---
layout: post
title: "Sonata Bundles Suite - Sonata Admin"
comments: true
---

Using Sonata Bundles Suite (Admin, User and Page) some install and use documentation.

Symfony standard Edition is not enough to build a small Content Management System. In an articles suite, we will discover how to use some of the most powerful Sonata Bundles. Today : Sonata Admin Bundle.

## Prerequisite

Our starting point is [a fresh Symfony 2 installation][Symfony install].

## Install

In your *composer.json* file, add :

{% highlight json %}
[...]
    "require": {
        [...],
        "sonata-project/admin-bundle": "dev-master",
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

Building a Content Management System with Sonata Page Bundle FTW. Today we'll see how to install this bundle and his dependencies.


## Pre-requisites

  - have a fresh Symfony 2 installation (see my [article for base Symfony install and setup][Symfony install])
  - if you want to version your code 'the git flow way' you can check [my article on how to use it](2013-10-02-git-the-gitflow-way)

## Why Sonata Page Bundle

Because (as of this writing) :

  - the awaited [Symfony CMF][Symfony CMF] is still not production ready, but it's moving very fast.
  - the Sonata bundles suite is well integrated. Only drawback is the documentation that is small and not up to date.

## Install

git flow feature start sonataPageBundle


## Documentation

[Sonata Admin Bundle][sonataAdminDoc]




[sonataAdminDoc]: http://sonata-project.org/bundles/admin/master/doc/index.html

[Symfony CMF]: http://cmf.symfony.com/

[Symfony install]: {% post_url 2013-08-25-how-to-make-a-base-symfony-installation-on-ubuntu %}

