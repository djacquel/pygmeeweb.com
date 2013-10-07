---
layout: post
title:  "Playing with github pages and Jekyll"
comments: true
---

I've just reinstalled my Ubuntu so also I need to reinstall [Github pages][ghpages]. Let's go !


## First ruby and gems

We install ruby 1.9.3 and Gems.

{% highlight bash %}
sudo apt-get update

sudo apt-get install ruby1.9.1 ruby1.9.1-dev \
  rubygems1.9.1 irb1.9.1 ri1.9.1 rdoc1.9.1 \
  build-essential libopenssl-ruby1.9.1 libssl-dev zlib1g-dev

# now try
ruby --version
# got ruby 1.9.3 ...
{% endhighlight %}

If you already have ruby 1.8 on your machine, your may encounter difficulties to make Jekyll work.
Look at [Leonard Ehrenfried's blog][leonard] for a quick fix.

## Second Jekyll

{% highlight bash %}
sudo gem install jekyll
{% endhighlight %}

## Set default structure

{% highlight bash %}
cd /whereourfileslives
jekyll new .
{% endhighlight %}

Et hop ! You have your new website structure.

In order to build your site the Github page way, you need the Gem.
Just create a _Gemfile_ file at the root, containing :
{% highlight ruby %}
source 'https://rubygems.org'
gem 'github-pages'
{% endhighlight %}

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

[leonard]: http://leonard.io/blog/2012/05/installing-ruby-1-9-3-on-ubuntu-12-04-precise-pengolin/
[jekyll]:    http://jekyllrb.com
[ghpages]: http://pages.github.com/
[ghpagesdoc]: https://help.github.com/categories/20/articles


