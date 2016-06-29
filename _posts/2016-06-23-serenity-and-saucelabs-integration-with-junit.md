---
layout: post
title:  "Serenity and SauceLabs integration with JUnit"
date:   2016-06-23 21:09:24 +0100
categories: serenity-bdd
tags: junit sauce-labs
---
## Problem

After hearing about [Serenity BDD](http://serenity-bdd.info/#/) and doing some simple tutorials I decided to evaluate it within my current project. 

It certainly has lots of nice features and personally I like the idea of  [Screenplay Pattern](https://www.infoq.com/articles/Beyond-Page-Objects-Test-Automation-Serenity-Screenplay) but because of its relatively not widely common adoption the documentation and community support is still not huge. So before introducing it to our project I wanted to evaluate it with  our basic scenario integrated with the CI environment. 

One of our features that needs to be supported is to run tests on [Sauce Labs](https://saucelabs.com/).
Fortunately Serenity supports this integration ( and also Browser Stack). After looking into [documentation](http://www.thucydides.info/docs/serenity/) I had working tests executed on Sauce Labs but the problem was that Sacue Labs didn't show results and name of the test. 

![alt text]({{ site.url }}/assets/posts/SL-unnamed-no-status-test.png "Unnamed and without status test result on SL")

### Basic configuration

We configure execution of tests in Sacue Labs in general Serenity conf file

	/src/test/resources/serenity.conf

Example of cofiguration for _latest_ version of firefox run on Vista machine on Sauce Labs


	webdriver {
	  driver = firefox
	  timeouts.implicitlywait = 10000
	}
	saucelabs {
	  user.id = <your value>
	  access.key = <your value>
	  url="http://"${saucelabs.user.id}":"${saucelabs.access.key}"@ondemand.saucelabs.com:80/wd/hub"
	  target.platform=vista
	  driver.version=latest
	  test.name = acceptance-tests
}

This is enough to run test on Sauce Labs under _acceptance-tests_ name

![alt text]({{ site.url }}/assets/posts/SL-running-test.png "Running tests on Sauce Labs")

## Informing Sauce Labs about test result

Sauce Labs _don't know_ what are our assertions in tests so we need to pass information about test result [separately](https://wiki.saucelabs.com/display/DOCS/Annotating+Tests+with+the+Sauce+Labs+REST+API).
With [SauceRest client](https://github.com/saucelabs/saucerest-java) that is fairly easy and you just need to capture test result from test runner. 
I used to work with TestNG where obtaining tests result was simple:
```java
@AfterMethod(alwaysRun = true)
public void writeResult(ITestResult result)
{
	//process result
}
```

JUnit doesn't provide such mechanism. But  we can use [Rules](https://github.com/junit-team/junit4/wiki/Rules) instead.

Before implementing it we need to be able to gather **current session ID** which will be used to target our test on Sauce Labs platform.

For that we can use Serenity's static method.

```java
Serenity.getCurrentSessionID();
```

### Passing test name to Sauce Labs

Another thing is to pass exact name of test case executed and not general one defined inside of Serenity.conf.

Using JUnit Rules we can get it from description object.

	description.getMethodName();

Additionally we capitalise first letter of test name extracted and remove underscores which are used for readability.

### Full Rule to update Sauce Labs jobs with relevant test name

```java
public class SauceLabsWatcher extends TestWatcher {

    SauceREST sauceRestClient;

    @Override
    public Statement apply(Statement base, Description description) {
        return super.apply(base, description);
    }

    @Override
    protected void succeeded(Description description) {
        sauceRestClient.jobPassed(Serenity.getCurrentSessionID());
    }

    @Override
    protected void failed(Throwable e, Description description) {
        sauceRestClient.jobFailed(Serenity.getCurrentSessionID());
    }

    @Override
    protected void finished(Description description) {
        updateSauceLabsTestName(description);
    }

    private void updateSauceLabsTestName(Description description) {
        Map<String, Object> sauceJob = new HashMap<>();
        sauceJob.put("name", prepareTestName(description));
        sauceRestClient.updateJobInfo(Serenity.getCurrentSessionID(), sauceJob);
    }

    private String prepareTestName(Description description) {
        String methodName = description.getMethodName().replace("_", " ");
        return methodName.substring(0, 1).toUpperCase() + methodName.substring(1);
    }

    @Override
    protected void starting(Description description) {
        super.starting(description);
        initialiseSauceRestClient();
    }

    private void initialiseSauceRestClient() {
        EnvironmentVariables loadedEnvironmentVariables = Injectors.getInjector().getProvider(EnvironmentVariables.class).get();
        String userID = getSauceLabsUserFrom(loadedEnvironmentVariables);
        String key = getSauceLabsKeyFrom(loadedEnvironmentVariables);
        sauceRestClient = new SauceREST(userID, key);
    }

    private String getSauceLabsKeyFrom(EnvironmentVariables loadedEnvironmentVariables) {
        return loadedEnvironmentVariables.getProperty(ThucydidesSystemProperty.SAUCELABS_ACCESS_KEY);
    }

    private String getSauceLabsUserFrom(EnvironmentVariables loadedEnvironmentVariables) {
        return loadedEnvironmentVariables.getProperty(ThucydidesSystemProperty.SAUCELABS_USER_ID);
    }
}
```

Having defined such Rule class we can use it in our tests simply by adding:

```java
@Rule
public SauceLabsWatcher sauceLabsWatcher = new SauceLabsWatcher();
```

Finally we can observe clearly marked tests on Sauce Labs dashboard:

![alt text]({{ site.url }}/assets/posts/SL-passed-and-failed-test.png "Test results on Sauce Labs")