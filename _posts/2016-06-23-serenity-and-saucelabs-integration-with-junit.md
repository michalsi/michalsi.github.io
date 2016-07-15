---
layout: post
title:  "Serenity and SauceLabs integration with JUnit"
date:   2016-06-23 21:09:24 +0100
categories: serenity-bdd
tags: junit sauce-labs
---

After hearing about [Serenity BDD](http://serenity-bdd.info/#/) and doing some simple tutorials I decided to evaluate it within my current project. 

It certainly has lots of nice features and personally I like the idea of  [Screenplay Pattern](https://www.infoq.com/articles/Beyond-Page-Objects-Test-Automation-Serenity-Screenplay) but because of its relatively not widely common adoption the documentation and community support is still not huge. So before introducing it into our project I wanted to evaluate it with our basic scenario integrated with the CI environment. 

One of our features that need to be supported is execution tests on [Sauce Labs](https://saucelabs.com/).
Fortunately Serenity supports it along with [BrowserStack]( https://www.browserstack.com/). After looking into [documentation](http://www.thucydides.info/docs/serenity/) I had working tests executed on Sauce Labs but the problem was that Sauce Labs wasn’t showing me results and names of the tests. 

![Unnamed and without status test result on SL]({{ site.url }}/assets/posts/SL-unnamed-no-status-test.png "Unnamed and without status test result on SL")

### Basic configuration

Serenity can be configured with single configuration file:

	/src/test/resources/serenity.conf

Part of configuration for _latest_ version of Firefox  and Vista machine on Sauce Labs

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

This is enough to run tests on Sauce Labs and display them with _acceptance-tests_ name

![Running tests on Sauce Labs]({{ site.url }}/assets/posts/SL-running-test.png "Running tests on Sauce Labs")

## Informing Sauce Labs about test result

Sauce Labs _doesn't know_ what are our assertions in tests so we need to pass information about test result [separately](https://wiki.saucelabs.com/display/DOCS/Annotating+Tests+with+the+Sauce+Labs+REST+API).
With [SauceRest client](https://github.com/saucelabs/saucerest-java) that is fairly easy and you just need to capture test result from test runner. 
I used to work with TestNG where obtaining tests result was simple:

```java
@AfterMethod(alwaysRun = true)
public void writeResult(ITestResult result)
{
	//process result
}
```

JUnit doesn't provide such mechanism. But we can use [Rules](https://github.com/junit-team/junit4/wiki/Rules) instead.

Before implementing it we need to be able to gather **current session ID** which will be used to distinguish our test on Sauce Labs platform.

We can use Serenity's static method.

```java
Serenity.getCurrentSessionID();
```

### Passing test name to Sauce Labs

Another thing I require is to display on Sauce Labs dashbord name of test case executed – its method's name. Not general one defined in Serenity.conf. I saw in the Serenity code that is should be done automatically but it didn’t work for me. So I decided to do something about it.

With JUnit we can again use _Rules_ where we can get current test name from description object.

	description.getMethodName();

Additionally we capitalise first letter of test name extracted and remove underscores that are used for readability.

### Rule to update Sauce Labs jobs status and test name

```java
public class SauceLabsWatcher extends TestWatcher {

    private boolean sauceLabsOn = false;

    private SauceREST sauceRestClient;

    @Override
    protected void succeeded(Description description) {
        if(sauceLabsOn){
            sauceRestClient.jobPassed(Serenity.getCurrentSessionID());
        }
    }

    @Override
    protected void failed(Throwable e, Description description) {
        if(sauceLabsOn) {
            sauceRestClient.jobFailed(Serenity.getCurrentSessionID());
        }
    }

    @Override
    protected void finished(Description description) {
        if(sauceLabsOn) {
            updateSauceLabsTestName(description);
        }
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

          if (isSauceLabsUrlProvided(loadedEnvironmentVariables)){
            sauceLabsOn = true;
            setupSauceRestClient(loadedEnvironmentVariables);
        }
    }

    private void setupSauceRestClient(EnvironmentVariables loadedEnvironmentVariables) {
        String userID = getSauceLabsUserFrom(loadedEnvironmentVariables);
        String key = getSauceLabsKeyFrom(loadedEnvironmentVariables);
        sauceRestClient = new SauceREST(userID, key);
    }

    private boolean isSauceLabsUrlProvided(EnvironmentVariables loadedEnvironmentVariables) {
        String url = loadedEnvironmentVariables.getProperty(ThucydidesSystemProperty.SAUCELABS_URL);
        return isNotEmpty(url);
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

![ Test results on Sauce Labs]({{ site.url }}/assets/posts/SL-passed-and-failed-test.png "Test results on Sauce Labs")
