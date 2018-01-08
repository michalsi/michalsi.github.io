---
layout: post
title:  "JMeter - Working with JSON"
date:   2018-01-08 19:37:59 +0100
categories: performance-tests
tags: jmeter json javascript
---

## Extract JSON value
First you probably want to get some JSON fro request response with JMeter's **JSON Extractor.** using JsonPath.
You can have a look at [this example](https://michalsi.github.io/performance-tests/2017/07/26/working-with-json-path.html) for that.

## JSON manipulation

As JSON naturally works with JavaScript we can use that language to craft something with it.
Let's use [JSR223 component](https://jmeter.apache.org/usermanual/component_reference.html#JSR223_Sampler) - you can use pre- or post- processors as well.

 ⚠️ You need to be aware of performance issue when using JavaScirpt compared to Groovy

JavaScript doesn't support `Cache compiled script if available` option.

	If checked (advised) and the language used supports Compilable interface (Groovy is one of these, java, beanshell and javascript are not), JMeter will compile the Script and cache it using it's MD5 hash as unique cache key

> The optional interface implemented by ScriptEngines whose methods
> compile scripts to a form that can be executed repeatedly without
> recompilation.

If we have our JSON saved in `personInfo` variable

```javascript
var personInfo = vars.get("personInfo");

//variable is saved in String - we need to convert it to JSON object
personObj = JSON.parse(personInfo);

//add new property pet : cat
personObj.pet = 'cat';
//modify existing key id => personId
personObj.personId = personInfo.id;
delete personObj.id;

//now we need to convert object back to String to save it in variable
log.info(JSON.stringify(personObj));

vars.put("updatedPersonalDetails", JSON.stringify(personObj) );
```