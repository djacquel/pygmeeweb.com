---
layout: post
title: "PHP cli debug with netbeans"
description: ""
category:
tags: []
published: false
comments: false
---

Sometimes you need to debug php cli commands (Symfony app/console, Drupal Drush).
This is the easy way to do it with netbeans.

This article is wrtiten for Unbuntu 14.04 and netbeans 8 but it may work with other OS and netbeans versions.

## Prerequisits

You have a running php with xdebug enabled.

Your xdebug setup is the following :

xdebug.remote_enable = on
xdebug.remote_handler = dbgp
xdebug.remote_port = 9000
xdebug.idekey = xdebug

I'll take the example of a Symfony app/console command.

Go to project window ProjectName>properties>run configuration

Here you are supposed to have a web config to do debuging with a web browser.
Let's setup a CLI debug.

In the panel do a Configuration > New
Enter a name for the new debug config > eg : Symfony CLI
Run As > Script
Index File > app/console
Arguments > cache:clear
PHP Options > -d idekey=netbeans-xdebug
