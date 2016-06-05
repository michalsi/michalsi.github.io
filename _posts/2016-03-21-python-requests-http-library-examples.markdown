---
layout: post
title:  "Python requests - HTTP library - examples"
date:  2016-03-21 21:09:24 +0100
categories: coding
tags: python
---

If you need to send some HTTP requests and that requires additional processing on your side maybe it'd be better to draft some script. If you happen to know python then you can try its [Requests](http://docs.python-requests.org/en/master/) library. Some simple snippets that can be used are placed further.

### Set up a session

```python
import requests
from requests import Request, Session
from requests.auth import HTTPBasicAuth

s = requests.Session()
```

### Send POST request:

```python
registrationData = {'registrationCode' :registrationCode ,'customerReference': customerRefNo}
 registrationResp=s.post(host + '/customer-account/register',headers = headers, data = registrationData)
```
 
Just add required headers e.g

```python
headers = {
 'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/29.0.1547.62 Safari/537.36'
}
```

### Send POST with json data

```python
authorisationPayload = {"personRoles":[{"role":"Owner","personId":userId}],
 "personPrivileges":[{"privilegeNames":
 ["Full permissions","Amend ","No access"],
 "personId":userId}]}
 
ra=s.post(host + '/organisation/' +str(businessID),headers = headers, json = authorisationPayload)
```

### Dealing with Cross-site request forgery

If we need to deal with XSRF token added to our requests we have to to extract it.

```python
def getXsrf(cookies):
    for cookie in s.cookies:
       if cookie.name =='XSRF-TOKEN':
          return cookie.value
```

and add it to our headers

```python
xsrf = getXsrf(s.cookies)
 headers['X-XSRF-TOKEN'] = xsrf
```

### Extract data from Json response:

```python
 if ( ra.status_code == 200 ):
   organisationID = ra.json()['_data'][0]['id'] 
```

In case of unverified HTTPS requests we can suppress warnings (if we really want it) :

```python
from requests.packages.urllib3.exceptions import InsecureRequestWarning
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)
```

We also probably need to add to our request this parameter  `verify=False`
to skip verification of HTTPS certificate. Use it if needed of course.