---
layout: post
title:  "Bash – script's arguments, find and work with files"
date:   2016-03-20 21:09:24 +0100
categories: linux
tags: bash
---
Recently I was working with bash script that was used to run performance tests on different environments from our CI server. It contained a few parts that I would like to save for later. 

## Variable number of arguments
`"$@"`
When the expansion occurs within double quotes, each parameter expands to a separate word. That is,`"$@"` is equivalent to `"$1" "$2" …`.

In that case we can work on variable number of arguments passed to our srcipt:

```bash
for arg in "$@"; do
    echo $arg
done
```

More info about [special arguments](https://www.gnu.org/software/bash/manual/html_node/Special-Parameters.html)

## Script's arguments - handling optional url

Let say we’d like to pass optional url to the script. We also have a number of environments that have the same pattern or urls and e.g. one which is different.

So the rules can look like this:

- If we’re providing explicit url – use it e.g. http://example.com
- If we’re providing variable with special environment e.g. test– use specific url 
- If we’re providing variable that matches our standard environments e.g. stage – create url with this variable i.e. www-example-stage.co.uk   

In bash such logic could look like this:

```bash
if [ "$1" != "" ]
then
    if [ ${1:0:4} = http ]
      then
 	URL=$1
    elif [ ${1} = "test" ]
    then
 	URL=www.ohter-test-example.co.uk
    else
 	URL=www-example-$1.dev.org.uk   
    fi
fi
```

## Setting paths in the script

```bash
WORKSPACE=`dirname $0`
RESULTS=$WORKSPACE/../results
```

`$0` Expands to the name of the shell or shell script. If Bash is invoked with a file of commands, $0 is set to the name of that file. 

## Get specific files from directory

Let’s say we have number of files with the same format in directory that we would like to process e.g.:

```
ls
page1.html page2.html page3.html
```

We can obtain them by following command:

```bash
FILES=$WORKSPACE/*.html
echo $FILES
```

output: 
`./page1.html ./page2.html ./page3.html`

### Get number of found files 

```bash
echo `find $FILES | wc -l`
```

output: `3`

We could also add additional arguments to the previous search to change directory depth of our search:
	 -maxdepth 1


### Get files names 

```bash
FILES_NAMES=`find $FILES | grep '[A-Za-z0-9]*\.html' -o | grep '[a-zA-Z0-9]*' -o`
```

This can be used further to loop through all found files

```bash
for file in $FILES_NAMES
    do
    	echo $file
    done
```

### Adding timestamp

It could be convenient to add time stamps and some parameters to variables that we use when creating a files. 

```bash
timestamp=ENV-"$ENV"_"`date +%s`
```
This added to a filename could produce something like that: 

`results_ENV-TEST_1458740243.txt`


## Extracting base name from a file
This can be useful especially if you operate on the list of files that are generated during run time and need to be processed further.

```bash
basename index.html .html
```

will output:
	`index`

Finally we can set up result file for each file found with previous commands: 

```bash
result=$RESULTS/`basename $file .html`$timestamp.log
```

## Writing to a file

`>` will delete content of the file and write new one:

```bash
echo "<html><head><title>Test results</title></head><body><h1>Tests results</h1>" > $RESULTS/results.html
```	

`>>` will append to a file

