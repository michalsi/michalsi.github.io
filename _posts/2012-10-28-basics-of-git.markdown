---
layout: post
title:  "Basics of git"
date:   2012-10-28 21:09:24 +0100
categories: tools
tags: git
---


A nice place to start learning about git: [http://git-scm.com/doc](http://git-scm.com/doc)

## Some basic commands

	$ git commit -a

Git automatically stage every file that is already tracked before doing the commit, letting you skip the git add part

* Removing files

If you simply remove the file from your working directory, it shows up under the _“Changed but not updated”_ (that is, unstaged) area of your git status.
The next time you commit, the file will be gone and no longer tracked.

To add a new remote Git repository as a short name you can reference easily, run:

	$ git remote add [shortname] [url]

You can use the `git pull` command to automatically fetch and then merge a remote branch into your current branch.

`$ git push origin master` - push your master branch to your origin server

`$ git checkout <branch>` To switch to an existing branch

`$ git checkout -b iss53` is a shorthand  for
```
$ git branch iss53
$ git checkout iss53
$ git merge
```

`$ git branch -d <branch>` deletes branch

`$ git mergetool `- fires up an appropriate visual merge tool and walks you through the conflicts

`$ git fetch origin `- looks up which server origin is, fetches any data from it that you don’t yet have, and updates your local database, moving your origin/master pointer to its new, more up-to-date position
