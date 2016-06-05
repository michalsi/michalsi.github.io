---
layout: post
title:  "Script to run Jmeter test plans and format HTML rapport"
date:   2016-05-25 21:09:24 +0100
categories: test-automation
tags: jmeter test-rapport
---
## Description 
This is a small script that runs jmeter with configured result parameters. It's part of our work that we've done around performance tests in one of the Kainos projects.

:star: Big kudos should go to the guys with whom I worked there! :star: 

###Features
+ Automatically detects tests plans that are placed in `tests` directory and run all of them.
+ Uses tools shipped with Jmeter (**CMDRunner** and **Xalan**) to parse results, prepare them in tabular form and generate graphs.
+ Different types of reports can be added, removed and configured in the script.
+ Creates HTML report for all tests scripts that has been run 
+ Displays collapsible images with performance metrics

There's also a link to 'Summary report' which is produced by separate process but is not included here at the moment.

### Demo

**Working Example** of report for 2 test plans and 4 types of charts can be viewed 
[here](https://rawgit.com/michalsi/jmeter-reports/master/results/results.html)

Source code is placed in [jmeter-reports](https://github.com/michalsi/jmeter-reports) repository


## Execution


Just run `runner.sh` from `scripts` directory.

Output is generated in `results` directory.
