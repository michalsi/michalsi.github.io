---
layout: post
title:  "Comparing two lists in python"
date:  2016-05-18 21:09:24 +0100
categories: coding
tags: python
---
Often I need to compare two lists to verify common or different values.With python we can do it pretty quickly## Show the same elements from lists
```pythona = [1,2,3,4,5]b = [0,2,3,5,6,5]	print(set(a).intersection(b))```
Output:	set([2, 3, 5])## Show differences in lists	
```pythona = [1,2,3,4,5]b = [0,2,3,5,6,5]	print(set(a).difference(b))```
Output: 	set([1, 4])