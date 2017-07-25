---
layout: post
title:  "Jmeter IF controller - example"
date:   2016-03-14 21:09:24 +0100
categories: test-automation
tags: jmeter
---

[If controller](http://jmeter.apache.org/usermanual/component_reference.html#If_Controller)  example to control which request to send based on response of the other sampler.

We're using [Dummy Sampler](http://jmeter-plugins.org/wiki/DummySampler/) to generate requests.

We also need to extract response's code. We can use [Regular Expression Extractor](http://jmeter.apache.org/usermanual/regular_expressions.htm) to get it:


* Field to check: Response Code
* Reference Name: RESPONSE_CODE
* Regular Expression: (\d+)
* Template $1$


First *If Controller* condition

	${RESPONSE_CODE}!="200"
	
Second *If Controller* condition:

	 ${RESPONSE_CODE}=="200"
	 
Setup Dummy Sampler to return code 200.

![alt text]({{ site.url }}/assets/dummy-sampler-200.png "Dummy Sampler - 200")

**Result:**

![alt text]({{ site.url }}/assets/if-controller-yes-condition.png "if controller - yes condition")

And now edit it to return something other than 200

![alt text]({{ site.url }}/assets/dummy-sampler-404.png "Dummy Sampler - 404")

**Result:**

![alt text]({{ site.url }}/assets/if-controller-result.png "If Controller result")


## Comparing variables 

In case you want to compare string variables you need to enclose them in quotes

	"${my_variable}"=="foo"

 











