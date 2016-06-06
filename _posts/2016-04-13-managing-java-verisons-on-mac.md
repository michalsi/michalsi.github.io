---
layout: post
title:  "Managing java versions on mac"
date:   2016-04-13 21:09:24 +0100
categories: osx
tags: java
---
Today I was strugling with java verions on my machine. After some time I realised that I had this problem before. Finally I was able to digg out sollution but that means that I need to save it here for myself fo the future.

## Jenv

[jenv](http://www.jenv.be/) is a small tool that can manage all java environment settings for you.


**List managed JDKs**

	$ jenv versions
	
output:

	system
	oracle64-1.6.0.39
	* oracle64-1.7.0.11 (set by /Users/user/.jenv/version)
	

**Configure global version**
	
	$ jenv global oracle64-1.6.0.39
	
**Configure local version (per directory)**

	$ jenv local oracle64-1.6.0.39
	
**Configure shell instance version**

	$ jenv shell oracle64-1.6.0.39



