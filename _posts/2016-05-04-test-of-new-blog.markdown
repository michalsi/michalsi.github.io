---
layout: post
title:  "Moving to Git hub pages with Jekkyl"
date:   2016-04-04 21:09:24 +0100
categories: blog
tags: jekyll github-pages
---

Recently I decided to move my blog from wordpress to github pages. That was actually a good opportunity to clean up old posts and start writing more new ones.

I was convinced by the nature of static websites and especially those that are hosted on Git hub pages. As github supports [Jekkyll](https://jekyllrb.com/) I did a quick research and found easy to use project which helps with that:[jekyllbootstrap](http://jekyllbootstrap.com/). The following points from its induction suited me when it comes to working with my own blog:

* Create content in markdown or textile.
* Manage everything with git.
* Publish from terminal.
* No database.
* No hosting headaches.

##Getting started with Jekyll-Bootstrap

I started with [quick start](http://jekyllbootstrap.com/usage/jekyll-quick-start.html) which sounded simple and promised to put aside any technical troubles with customisation of jekyll. I tried first, default theme and it worked just fine. But when I wanted to switch theme, which needs only 2 commands:
to install theme:

{% highlight bash %}
	rake theme:install name="THEME-NAME"
{% endhighlight %}

and switch to it:
{% highlight bash %}
	rake theme:switch name="the-program"
{% endhighlight %}	
I got broken styles on my the page. I found out that many people had similar issues and even pull request was submitted to the original project with the fix. It still waits for merge when I'm writing it and generally author hasn't done much during last 2 years. So I decided that it will be better to dig out through a standard way of working with jekyll which included [liquid](https://github.com/Shopify/liquid/wiki).

## Trying simpler solution

I decided to start with some nice looking theme here and found [paper](https://github.com/dbtek/paper).
It serves most of my needs and has potential for enhancements, which hopefully I'll soon post here.

The only one thing that I had to do additionally was changing highliter from _pygments_ to _rouge_ according to [this](https://help.github.com/articles/page-build-failed-config-file-error/#fixing-highlighting-errors) warning from github pages.
And needed to install it:
{% highlight bash %}
gem install rouge
{% endhighlight %}	

So far I'm really happy from migration to github pages with jekyll.


