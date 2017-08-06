---
layout: post
title:  "Setting and removing variables in Jmeter"
date:   2016-04-06 21:09:24 +0100
categories: performance-tests
tags: jmeter java-script
---


To set the variable in Jmeter we use just `vars.put("variable","value")`

```javascript
vars.put("user_1", "john");
vars.put("user_2", "bob");
```

Of course each separate thread has its own set of values.

Recently I noticed one defect in our script. We were creating number of variables i.e. `user_1, user_2, user_3` etc. But the problem was that for every iteration this list should generate different number of variables. 

We can easily override previous values just by assigning a new value. 
But the issue was when current number of values is smaller then previous one i.e. we got 3 user variables and now we need to write only 2. Jmeter doesn't clean variables on its own and keeps user_3 set from previous run which coused problem.

One sollution that I found would be to clean all variables with following code:

```java
import org.apache.jmeter.threads.JMeterVariables; 
	
JMeterVariables clean = new JMeterVariables(); 
vars = clean;
```

Looks nice but unfortunatley didn't work in my case. I tried to put it into BeanShell Sampler but without luck.

One of the option people recomended would be to use RegEx extractor with Match No=-1. You can clear all generated values by applying RegEx Extractor to an empty input.
For example you can add empty BeanShell sampler and child RegEx extractor at the beginning of the thread. 

As another option was to iterate through your variables in BeanShell sampler and initialize or remove them. 

To remove variable:

```javascript
vars.remove(varName);
```

To test it with BSFSampler and javascript: 

```javascript
vars.put("user_1", "john");
vars.put("user_2", "bob");
	
var i =1;
while (vars.get("user_"+i)) {
	log.info("user_" + i + " - " + vars.get("user_"+i));
	vars.remove("user_" + i);   	
   	log.info(vars.get("user_" + i));
   	i++;
}
```
	
and the output was:

	INFO  - jmeter.protocol.java.sampler.BSFSampler: user_1 - john 
	INFO  - jmeter.protocol.java.sampler.BSFSampler:  
	INFO  - jmeter.protocol.java.sampler.BSFSampler: user_2 - bob  
	INFO  - jmeter.protocol.java.sampler.BSFSampler:  
	
	
And the same in ready to use function:

```javascript
function removeVariables(varName) {
	var i =1;
	while (vars.get(varName + "_" + i)) {
		vars.remove(varName + "_" + i);  
		i++;
	}	
	return;
}
```