---
layout: post
title: "Files permissions for a PHP web application"
description: ""
category:
tags: []
published: true
---











follow run these commands to do so

sudo chown -R www-data:www-data /home/supercomputer/Desktop/PHP

sudo chmod -R 775 /home/supercomputer/Desktop/PHP
At last add yourself to www-data group

sudo adduser yourUserName www-data