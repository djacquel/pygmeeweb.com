---
layout: post
title: "Jekyll cheat sheet"
description: "Here a description"
category:
tags: []
published: true
date: 2013-08-10
---

Personal useful commands for [Jekyll][Jekyll]

##create a new post
Inspired by [Jekyll bootstrap Rakefile][Jekyll bootstrap rakefile]
{% highlight bash %}
rake post title="Post's title here" tags="tagOne tagTwo" date="2012-12-31"
{% endhighlight %}

## Building site
{% highlight bash %}
jekyll build --drafts
jekyll build --watch
jekyll build --source _source --destination _deploy
jekyll build --config _config.yml,_config-dev.yml --drafts
{% endhighlight %}

[Jekyll]:    http://jekyllrb.com
[Jekyll bootstrap]: http://jekyllbootstrap.com/
[Jekyll bootstrap rakefile]: https://github.com/plusjade/jekyll-bootstrap/blob/master/Rakefile
