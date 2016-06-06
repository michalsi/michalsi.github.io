---
layout: post
title:  "Working with files in python"
date:   2016-05-06 21:09:24 +0100
categories: coding
tags: python
---
In many cases we need to read from or write to a file. Python has simple mechanisms that we can use for that purpose.## Delete file and create new one with initiall text```pythondef prepareOutputFile():		with open(outputFile, "w") as myfile:		myfile.write( "UserID,BusinessID\n" )   	return```
## Append text to a file
```pythondef writeOutput( string ):	with open(outputFile, "a") as myfile:		myfile.write( string )    	return```## Open file and process line by line:
```pythonwith open(inputFile) as f:	next(f) # skip header	for line in f:		print line```
### Stripping the newline character:
read a file line by line into a list 
```pythonusersList = [line.rstrip('\n') for line in open(inputUsersFile)]
```