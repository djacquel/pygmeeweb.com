---
layout: post
title: "Files permissions for a PHP web application"
---


follow run these commands to do so

sudo chown -R www-data:www-data /home/supercomputer/Desktop/PHP

sudo chmod -R 775 /home/supercomputer/Desktop/PHP
At last add yourself to www-data group

sudo adduser yourUserName www-data



[Symfony install]: {% post_url 2013-08-25-how-to-make-a-base-symfony-installation-on-ubuntu %}
[sontaAdminInstall]: {% post_url 2013-10-03-sonata-bundles-suite-sonata-admin-installation %}
