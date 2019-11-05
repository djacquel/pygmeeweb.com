---
title: "Composer cheat sheet"
comments: false
---

Usefull commands for composer

## WORK IN PROGRESS

[Composer documentation][Composer]
[Packages for composer : Packagist.org][Packagist]

## Install globally on *nix

{% highlight bash %}
curl -sS https://getcomposer.org/installer | php
# for ubuntu we sudo
sudo mv composer.phar /usr/local/bin/composer
{% endhighlight %}

You can now invoke _**composer**_ from anywhere.

## options for all commands

**Not all commands are referenced here** for a more exhaustive view please

see [Composer commands documentation][Composercli].

**-v** verbose | **-h** help | **-q** no output | **-n** no interaction
**--profile** timing and memory usage | **-V** version

## Commands

| Command        | Wat!           |
| ------------- |-------------|
| composer | list available commands |
| composer init -h      | print help on init command |
| - | - |
| composer init | launch interactive composer.json creation |
| composer require | add requirement to composer.json |
| composer require --no-update | Disables the automatic update of the dependencies |

## version

| version | span |
| --- | --- |
| 1.0.2 | 1.0.2 |
| >, >=, <, <=, != | >1.0 // multi >=1.0,<2.0 |
| 1.0.* | >=1.0,<1.1 |
| ~1.2 |>=1.2,<2.0 |
| ~1.2.3 | >=1.2.3,<1.3 |

{% include _links_library.markdown %}
