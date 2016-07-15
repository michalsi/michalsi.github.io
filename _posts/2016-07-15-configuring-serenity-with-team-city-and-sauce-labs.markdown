---
layout: post
title:  "Configuring Serenity with Team City and Sauce Labs"
date:   2016-07-15 21:09:24 +0100
categories: serenity-bdd
tags: sauce-labs team-city
---

When working with atomated test we most likely would like to hook them up into some Continuous Integration environment.

Recently I wanted to integrate [Serenity](http://serenity-bdd.info/#/) tests into Team City server and run them on [Sauce Labs](https://saucelabs.com/platforms/). 

My main requirements were:

1. Tests execution on demand from Team City 
2. Tests execution as an automatic step of build
3. Possibility to chose driver, driver version and platform to use on Sauce Labs

Points 1 and 2 have been fulfilled mostly by creating and configuring a standard [build step](https://confluence.jetbrains.com/display/TCD9/Creating+and+Editing+Build+Configurations) in Team City. 

As for point 3 we need to tell Serenity what configuration it should use. This is set in `serenity.conf` file. 

When executing tests locally any changes needed are easy to make - just edit conf file.
From Team City we need to do that change in some other way.

I can see 2 options:

a. Prepare number of files with different configuration and replace main config file with selected one
b. Edit main config file. 

Option _a_ would suit my second requirement. We could prepare different builds e.g. to run Firefox with Windows, Chrome with Mac etc. Before executing tests we can simply replace standard Serenity.conf file with the one which has defined desirable properties .

Option _b_ would be good for first requirement. We just need to have separate mechanism to pass configuration set by user in Team City and change corresponding entries in Serenity.conf.

Let assume such configuration in Serenity.conf:

    webdriver {
        driver = chrome
    }
    
    saucelabs {
        target.platform = WIN10
        driver.version = latest 
    }

## Passing configuration variables from Team City

One of the option is to use environment variables. User sets such variable as parameter for executed build. 

![team city env variables]({{ site.url }}/assets/posts/team-city-env-variables.png "team city env variables")

Example of parameters configuration;

![tc-edit-parameter]({{ site.url }}/assets/posts/tc-edit-parameter.png "tc-edit-parameter")

I don't want to put any logic behind setting environment variables i.e. limiting not possible configuration. I leave it to the user and his common sense ;)

The current list of possible configuration is available [here](https://saucelabs.com/platforms/)

## Changing config file

Now we can read environment variable and change corresponding entry in serenity.conf.

My first attempt was to use [sed](http://michalsi.github.io/linux/2015/04/03/unix-quotes-and-sed.html) with sh script. But decided to abandon this idea in favour of better maintainability of python script (at leas from my perspective).

So my script has list of properties to change.
Reads values from environment variables and replace corresponding lines in serenity.conf file accordingly.

Simple implementation to modify target.platform, driver, driver.version


```python
#! /usr/bin/env python

"""
Scritp to modify serenity.conf file.
It updates given key - value pairs
"""

from tempfile import mkstemp
from shutil import move
from os import remove, close
import re
import os


original_path = './src/test/resources/serenity.conf'

def is_pattern_found(pattern, line):
    regex_pattern = r'^' + re.escape(pattern) + r'='
    striped_line = "".join(line.split())
    return re.search(regex_pattern, striped_line, re.M|re.I)


def modify(pattern, subst):
    #Create temp file
    fh, abs_path = mkstemp()
    with open(abs_path,'w') as new_file:
        with open(original_path) as old_file:
            for line in old_file:
                # skip comment lines
                if line.strip().startswith('//') or line.strip().startswith('#'):
                    new_file.write(line)
                elif is_pattern_found(pattern,line):
                    new_file.write('\t' + pattern + ' = ' + subst + '\n')
                        print 'Modified property: ' + pattern + ' with value: ' +  subst + '\n'
                else:
                    new_file.write(line)
    close(fh)
    #Remove original file
    remove(original_path)
    #Move new file
    move(abs_path, original_path)


def modify_serentiy_conf(properties):
    for property in properties:
        print 'Taken property: ' + property
        try:
            os_env = os.environ[property]
        except KeyError, e:
            print 'KeyError exception returned - reason %s' % str(e)
            continue

        if os_env:
            serenity_prop = property.replace("_", ".").lower()
            print 'Serentiy property: ' + serenity_prop
            modify(serenity_prop, os_env)

properties = ["Target_Platform","Driver", "Driver_Version"]

modify_serentiy_conf(properties)
```

### Possible platforms 

As additional information following platforms were troublesome for me when executing on Sauce Labs

 - SNOW_LEOPARD, MOUNTAIN_LION
For those Sauce Labs returned errors when trying to spin up Chrome browser.

- UNIX is the same as LINUX
- ANY is not supported by Sauce Labs
