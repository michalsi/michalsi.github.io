---
layout: post
title:  "How to execute JMeter tests andreports"
date:   2018-01-15 19:37:59 +0100
categories: performance-tests
tags: jmeter maven
---

## How to execute JMter tests

### With Maven
[JMeter maven plugin](https://github.com/jmeter-maven-plugin/jmeter-maven-plugin) can be used to manage tests execution and their configuration. It also downloads [JMeter plugins](https://jmeter-plugins.org/) that are required.

To run test script use following command from the root directory of your repository with jmeter tests:

	mvn -Djmeter.test.file=<test_name_without_jmx> verify

	
Tasks that will be executed by maven plugin:

1. 	Copy all of the JMX test files held in the ***src/test/jmeter/*** directory, and sub-directories, to ***target/jmeter/testFiles***. 
2. 	Execute tests from ***target/jmeter/testFiles***.
3.  Save JTL output file in ***/target/jmeter/results***
4.  Generate HTML test report based on JTL file in ***/target/jmeter/results/html***


### With JMeter binary

#### Ho to get JMeter

_Recomended way is to use maven:_

	mvn install -Dskip.report.generation=true
	
Jmeter will be downloaded to ***target/jmeter/bin***.

You can also download it manually from [Jmeter dowload](http://jmeter.apache.org/download_jmeter.cgi) page, but then you also need to add JMeter plugins separatley.

#### Running tests without Maven

	java -jar ApacheJMeter.jar -n -t test-file [-p property-file] [-l log-file.jtl] 
	
	-n - This specifies JMeter is to run in non-gui mode
	-t - name of JMX file that contains the Test Plan
	-l - name of JTL file to log sample results to
	-j - name of JMeter run log file

### With JMeter GUI

> **GUI mode is for script creation and debugging, not for load testing !**

You can open JMeter GUI - ApacheJMeter.jar from jmeter bin directory

## Resutls Analysis

JMeter saves output in raw format in jtl files. This can be used to prepare HTML report 

### Running with Maven

Maven saves JTL files in `target/jmeter/results`.

HTML report is generated in `target/jmeter/results/html`.

When running tests locally, HTML report will not be generated if `target/jmeter/results/html` direstory is not empty.

### Generating HTML report manually

 	java -jar ApacheJMeter.jar -g <path_to_jtl_file> -o <path_to_place_results>
 	

If `-o <path_to_place_results>` not provided then `/tmp/test-report` is used


## Pom example used to run JMeter tests

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>performance-tests</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>Performance Tests</name>
    <properties>
        <jmeter.version>3.2</jmeter.version>
        <jmeter.maven.plugin.version>2.4.0</jmeter.maven.plugin.version>
        <jmeter.test.file>debug-plan</jmeter.test.file>
        <maven.build.timestamp.format>yyyyMMdd</maven.build.timestamp.format>
        <timestamp>${maven.build.timestamp}</timestamp>
        <jmeter.directory>${basedir}/target/jmeter</jmeter.directory>
        <jmeter.dashboard.dir>${jmeter.directory}/results/html</jmeter.dashboard.dir>
        <skip.report.generation>false</skip.report.generation>
    </properties>
    <build>
        <plugins>
            <plugin>
                <groupId>org.codehaus.gmaven</groupId>
                <artifactId>gmaven-plugin</artifactId>
                <version>1.5</version>
                <executions>
                    <execution>
                        <id>add-dynamic-properties</id>
                        <phase>initialize</phase>
                        <goals>
                            <goal>execute</goal>
                        </goals>
                        <configuration>
                            <source>
                               def testReport = project.properties['jmeter.test.file'].replaceAll('/','_')
                               project.properties['jmeter.test.report']= testReport
                            </source>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
             <plugin>
                <artifactId>maven-resources-plugin</artifactId>
                <version>3.0.1</version>
                <executions>
                    <execution>
                        <id>copy-resources-1</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>copy-resources</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>${jmeter.directory}/bin</outputDirectory>
                            <resources>
                                <resource>
                                    <directory>src/test/resources</directory>
                                    <includes>
                                        <include>reportgenerator.properties</include>
                                    </includes>
                                </resource>
                                <resource>
                                    <directory>src/test/resources/data-load</directory>
                                    <includes>
                                        <include>*.csv</include>
                                    </includes>
                                </resource>
                            </resources>
                        </configuration>
                    </execution>
                    <execution>
                        <id>copy-resources-2</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>copy-resources</goal>
                        </goals>
                        <configuration>
                            <skip>${skip.report.generation}</skip>
                            <outputDirectory>${jmeter.directory}/bin/report-template</outputDirectory>
                            <resources>
                                <resource>
                                    <directory>src/test/resources/report-template</directory>
                                    <targetPath>${jmeter.directory}/bin/report-template</targetPath>
                                </resource>
                            </resources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>com.lazerycode.jmeter</groupId>
                <artifactId>jmeter-maven-plugin</artifactId>
                <version>${jmeter.maven.plugin.version}</version>
                <executions>
                    <execution>
                        <id>jmeter-tests</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>jmeter</goal>
                        </goals>
                        <configuration>
                            <testFilesIncluded>
                                <jMeterTestFile>${jmeter.test.file}.jmx</jMeterTestFile>
                            </testFilesIncluded>
                            <resultsFileNameDateFormat>${maven.build.timestamp.format}</resultsFileNameDateFormat>
                            <jMeterProcessJVMSettings>
                                <xms>4096</xms>
                                <xmx>4096</xmx>
                                <arguments>
                                    <argument>-Xprof</argument>
                                    <argument>-Xfuture</argument>
                                </arguments>
                            </jMeterProcessJVMSettings>
                        </configuration>
                    </execution>
                </executions>
                <configuration>
                    <jmeterVersion>${jmeter.version}</jmeterVersion>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.5.0</version>
                <executions>
                    <execution>
                        <id>generate-report-dashboard</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>exec</goal>
                        </goals>
                        <configuration>
                            <skip>${skip.report.generation}</skip>
                            <executable>java</executable>
                            <arguments>
                                <argument>-jar</argument>
                                <argument>${jmeter.directory}/bin/ApacheJMeter-${jmeter.version}.jar</argument>
                                <argument>-g</argument>
                                <argument>${jmeter.directory}/results/${timestamp}-${jmeter.test.report}.jtl</argument>
                                <argument>-o</argument>
                                <argument>${jmeter.dashboard.dir}</argument>
                            </arguments>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    <profiles>
        <profile>
            <id>remote</id>
            <properties>
                <jmeter.server.list>invalid.serverList</jmeter.server.list>
            </properties>
            <build>
                <plugins>
                    <plugin>
                        <groupId>com.lazerycode.jmeter</groupId>
                        <artifactId>jmeter-maven-plugin</artifactId>
                        <version>${jmeter.maven.plugin.version}</version>
                        <configuration>
                            <remoteConfig>
                                <startServersBeforeTests>true</startServersBeforeTests>
                                <serverList>${jmeter.server.list}</serverList>
                                <stopServersAfterTests>true</stopServersAfterTests>
                            </remoteConfig>
                        </configuration>
                    </plugin>
                </plugins>
            </build>
        </profile>
        <profile>
            <id>plugins-setup</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-dependency-plugin</artifactId>
                        <version>3.0.2</version>
                        <executions>
                            <execution>
                                <id>copy</id>
                                <phase>package</phase>
                                <goals>
                                    <goal>copy</goal>
                                </goals>
                                <configuration>
                                    <artifactItems>
                                        <artifactItem>
                                            <groupId>kg.apc</groupId>
                                           <artifactId>jmeter-plugins-standard</artifactId>
                                            <version>1.4.0</version>
                                            <overWrite>true</overWrite>
                                            <outputDirectory>${jmeter.directory}/lib/ext</outputDirectory>
                                        </artifactItem>
                                        <artifactItem>
                                            <groupId>kg.apc</groupId>
                                           <artifactId>jmeter-plugins-json</artifactId>
                                            <version>2.6</version>
                                            <overWrite>true</overWrite>
                                            <outputDirectory>${jmeter.directory}/lib/ext</outputDirectory>
                                        </artifactItem>
                                    </artifactItems>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>
</project>
```
