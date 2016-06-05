---
layout: post
title:  "Jmeter base directory and script variables"
date:   2016-03-09 12:09:24 +0100
categories: test-automation
tags: jmeter
---

## Working with Been Shell components
One of the option to work with variables in Jmeter is to use [BeanShell](http://beanshell.org/) components like [BeanShell PreProcessor](http://jmeter.apache.org/usermanual/component_reference.html#BeanShell_PreProcessor) .

### Getting JMeter base dir
```java
import org.apache.jmeter.services.FileServer;

String baseDir = FileServer.getFileServer().getBaseDir();
vars.put("baseDir", baseDir);
```

### Getting JMeter's script location (GUI mode)
```java
import org.apache.jmeter.gui.GuiPackage;

String testPlanFile = GuiPackage.getInstance().getTestPlanFile();
vars.put("testPlanFile", testPlanFile);
```

### Getting JMeter's script name (non-GUI mode)
```java
import org.apache.jmeter.services.FileServer;

String scriptName = FileServer.getFileServer().getScriptName();
vars.put("scriptName", scriptName); 
```

## Using inline function
If you prefer you can get the same using [__Beanshell()](https://jmeter.apache.org/usermanual/functions.html#__BeanShell) function as

```java
${__BeanShell(import org.apache.jmeter.services.FileServer; FileServer.getFileServer().getBaseDir())}
```

and it will return current base directory where it is called.
[source](http://stackoverflow.com/a/28110434/1037754)