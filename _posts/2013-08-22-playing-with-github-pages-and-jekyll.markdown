---
layout: post
title:  "Playing with Github pages and Jekyll"
comments: true
---

{% include _links_library.markdown %}
Installing and using Jekyll in five minutes.

As I'm often installing Ubuntu boxes, I needed a quick reminder to setup jekyll.
This is how to get Jekyll running for [Github pages][ghpages] in five minutes.

## Installing ruby and gems

We install ruby 1.9.3 and Gems.

{% highlight bash %}
# Ubuntu 12.04
sudo apt-get update
sudo apt-get install ruby1.9.1 ruby1.9.1-dev \
  rubygems1.9.1 irb1.9.1 ri1.9.1 rdoc1.9.1 \
  build-essential libopenssl-ruby1.9.1 libssl-dev zlib1g-dev
{% endhighlight %}

{% highlight bash %}
# Ubuntu 14.04
sudo apt-get update
sudo apt-get install ruby ruby1.9.1-dev ri1.9.1 node-js
{% endhighlight %}

If you already have ruby 1.8 on your machine, your may encounter difficulties to make Jekyll work.
Look at [Leonard Ehrenfried's blog][leonard] for a quick fix.

## Bundler
This gem will help update gems with a one line command.
{% highlight bash %}
sudo gem install bundler
{% endhighlight %}

## Jekyll
{% highlight bash %}
sudo gem install jekyll
{% endhighlight %}

## Generate files default structure

{% highlight bash %}
# go to your working folder
cd /whereourfileslives
# create a new site in this folder
jekyll new .
{% endhighlight %}

Et hop ! You have your new website structure.

In order to build your site the Github page way, you need the Gem.
Just create a __Gemfile__ file at the root, containing :
{% highlight ruby %}
source 'https://rubygems.org'
gem 'github-pages'
{% endhighlight %}

Install github-pages gem
{% highlight ruby %}
bundle install
{% endhighlight %}

You are now ready to use Jekyll.

## Generating static pages

As this default site already contains a post you can already _build_ your static site.

{% highlight bash %}
jekyll build
{% endhighlight %}

This will create a __site__ folder containing all necessary files for your site.

You can now point a virtual hosts to *whereourfileslives/_site* and see the magic.

See [Github pages documentation][ghpagesdoc] for more info to setup your site and [Jekyll site][jekyll] to quickly build a nice static marvel.

## Some permissions

{% highlight bash %}
find whereourfileslives/ -type d -exec chmod 755 {} \;
find whereourfileslives/ -type f -exec chmod 644 {} \;
{% endhighlight %}

{% include _links_library.markdown %}
