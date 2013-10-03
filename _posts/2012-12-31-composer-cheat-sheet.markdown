---
layout: post
title: "Composer cheat sheet"
published: true
---

Usefull commands for composer

[Composer documentation](http://getcomposer.org/doc/)

## Install globally on *nix

{% highlight bash %}
curl -sS https://getcomposer.org/installer | php
# for ubuntu we sudo
sudo mv composer.phar /usr/local/bin/composer
{% endhighlight %}

You can now invoke _**composer**_ from anywhere.

## options for all commands

**Not all commands are referenced here** for a more exhaustive view please see [Composer commands documentation](http://getcomposer.org/doc/03-cli.md).

**-v** verbose | **-h** help | **-q** no output | **-n** no interaction
**--profile** timing and memory usage | **-V** version

| Command        | Comments           |
| ------------- |-------------|
| composer | list available commands |
| composer init -h      | print help on init command |
| - | - |
| composer init | launch interactive composer.json creation |
| composer require | add requirement to composer.json |
| composer require --no-update | Disables the automatic update of the dependencies |


<a href="http://example.com/" target="_blank">Hello, world!</a>