---
layout: post
title:  "Jmeters Variables From CSV File - relative path"
date:   2016-03-10 21:09:24 +0100
categories: test-automation
tags: jmeter
---

Jmeters Variables From CSV File config element set its base directory as a user's home directory. If you _Browse_ for your file it will put something like:

	/Users/your_user/./your_file.txt

So in such case we need to pass base directory to be able to work with relative paths for our files.

1. Define base directory 'baseDir' in User Defined Variables:

```java
	${__BeanShell(import org.apache.jmeter.services.FileServer; FileServer.getFileServer().getBaseDir())}
```

2. Use it in your element:

		${baseDir}/../data/your_data.csv
