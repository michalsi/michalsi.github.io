---
layout: post
title:  " Working with Jekyll - switch to 'minima' theme"
date:   2017-07-19 19:37:59 +0100
categories: blog
tags: jekyll
---

After a while of being busy with other stuff I wanted to get back to my notes kept on github pages. First thing I wanted to do was make some cleanup and fixes. I got advise from github about officially supported themes and the one which I used wasn't on that list.
I decided to look for something new. 

## Installation on mac
Make sure you've got `ruby` set up. E.g. type `ruby -v`

Install jekyll:
`gem install --user-install bundler jekyll`

Go to [official docs](https://jekyllrb.com/docs/installation/macos/) for details.


## Start a new blog.

	jekyll new michalsi.github.io

It will have default [minima](https://github.com/jekyll/minima) theme installed. It suited well most of my needs for a clean and clear theme.
 With *just a few tweaks* it should preserve all of my blog's functionalities and give it a fresh look.

## Run Jekyll server
This command will watch for changes automatically.	
	
	jekyll serve

Same as `jekyll serve` but will detach from the current terminal.

	jekyll serve --detach

## Theme customisation

### Categories
    
To add categories and tags pages we need to include following liquid code
    
    {% raw  %}
	{% for cat in site.categories %}
	    <li>{{ cat[0] }}</li>
	{% endfor %}
	{% endraw %}


### Pagination

To limit lists of posts displayed at the same time on the main page we need to add pagination.

Add into `_config.yml`

	gems: [jekyll-paginate]
	
	paginate: 5
	paginate_path: "/page-:num/"

Basic pagination for your page is explained [here](https://jekyllrb.com/docs/pagination/)