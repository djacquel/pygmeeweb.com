---
layout: post
title: "Contributing on a forked Symfony project but also on vendors"
description: ""
category:
tags: []
published: false
comments: false
---

As a Symfony based project contributor it sometimes happened that I need to make changes to one of my dependencies (in vendor folder).

The problem is that you need to make your changes on a forked vendor, make a pull request and wait for the repository manager to merge your PR.

You sometimes cannot rely on this merge to go on, so you have to work with your forked version instead of the original one.
And for this, you need to change your composer.json to point to your fork.
But the composer.json is itself part of you main forked repository and it is versioned !


## Setting up the project


## References
Stack overflow question [Contributing to open source bundles from vendor directory?](http://stackoverflow.com/questions/13325158/contributing-to-open-source-bundles-from-vendor-directory)
