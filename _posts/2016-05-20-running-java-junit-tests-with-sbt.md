---
layout: post
title:  "Running Java JUnit tests with SBT"
date:   2016-05-20 21:09:24 +0100
categories: test-automation
tags: sbt junit
---
This is sample configuration to run JUnit tests with [SBT](http://www.scala-sbt.org/)


**built.sbt**

```scala
// Project name (artifact name in Maven)
name := " System Tests"
	
version := "1.0-SNAPSHOT"

description := " System test project"
	
// This forbids including Scala related libraries into the dependency
autoScalaLibrary := false
	
// http://mvnrepository.com/artifact/junit/junit
libraryDependencies += "junit" % "junit" % "4.12"
	
libraryDependencies += "com.novocode" % "junit-interface" % "0.10" % "test"
	
//docs https://github.com/sbt/junit-interface
testOptions += Tests.Argument(TestFrameworks.JUnit, --tests=<REGEXPS>)
```

Important part is to add and configure [junit-interface](https://github.com/sbt/junit-interface) which allows us to run junits from sbt.

_[source](http://xerial.org/blog/2014/03/24/sbt/)_

I also use custom script with some configuration parameters to run sbt jar that is uploaded into the project

	java -Xms512M -Xmx1536M -Xss1M -XX:+CMSClassUnloadingEnabled -XX:MaxPermSize=384M -jar `dirname $0`/sbt-launch.jar "$@"

and then I can execute clean run of tests

	./sbt clean compile test
	
Here's [sample project](https://github.com/michalsi/sbt-java-tests) whit junit tests and sbt.

