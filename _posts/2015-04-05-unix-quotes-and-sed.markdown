---
layout: post
title:  "Unix quotes regular expressions and sed"
date:   2015-04-03 21:09:24 +0100
categories: linux
tags: bash regular-expressions sed
---

I found following links as a good source of Unix tutorials

* [Quotes](http://www.grymoire.com/Unix/Quote.html)
* [Regular Expressions](http://www.grymoire.com/Unix/Regular.html) 
* [Sed - An Introduction and Tutorial](http://www.grymoire.com/Unix/Sed.html)

## Quick extract about Sed

Suppose you have the input file:

	one two three, one two three
	four three two one
	one hundred

and you used the command

	sed 's/one/ONE/' <file

The output would be

	ONE two three, one two three
	four three two ONE
	ONE hundred

There are four parts to this substitute command:

	s	  Substitute command
	/../../	  Delimiter
	one	  Regular Expression Pattern Search Pattern
	ONE	  Replacement string


Example to replace 'abc' string in the file into value from environmnent variable (run on mac)

```bash
sed -i -e "s_abc_${HOME}_g" file
```

### Replace string in file with environmnent variable found with regular expressions 

file:

	saucelabs {
	  url="http://"${saucelabs.user.id}":"${saucelabs.access.key}"@ondemand.saucelabs.com:80/wd/hub"
	  target.platform=vista
	  driver.version=latest
	  test.name = acceptance-tests
	  user.id = <user>
	  access.key = <key>
	}

command 

```bash
sed -i -e -E "s_(test.name[ ]?=[ ]?) (.*)_\1 ${HOME}_g" file
```

will have effect of: 


	test.name = /Users/michals

