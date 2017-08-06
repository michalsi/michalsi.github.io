---
layout: post
title:  "JMeter - working with JsonPath"
date:   2017-07-26 19:37:59 +0100
categories: performance-tests
tags: jmeter jsonpath
---

## JSON Path Extractor

JMeter provides [JSON Path Extractor](https://jmeter-plugins.org/wiki/JSONPathExtractor/)
component which extracts nodes from JSON response with JsonPath.

### Filter specific JSON node with JsonPath

To filter nodes we need to use `?()`.
 
`@` defines current object/element.

#### Example

Get all `Events` that has specific `correlation.client` id and then get one with specific name.

Then get `caseId` which is included in that event.

	$.events[?(@._metadata.correlation.client=="445ceccf-34bb-470f-a345-8be3f4e3ff2f"
	 && @._metadata.name=="public.resulting.case-to-be-resulted-assigned")].caseId


Json that needs to be processed:

	{  
	   "events":[  
	      {  
	         "_metadata":{  
	            "correlation":{  
	               "client":"445ceccf-34bb-470f-a345-8be3f4e3ff2f"
	            },
	            "context":{  
	               "user":"a085e359-6069-4694-8820-000000000000"
	            },
	            "stream":{  
	               "id":"19151ca7-d755-4005-b76c-483309475c39",
	               "version":1
	            },
	            "id":"b5bc10a3-22f5-4cfb-b537-375b0697cdf3",
	            "name":"assignment.assignment-created",
	            "causation":[  
	               "c925e708-c61b-4039-b1ea-0c33f233619a",
	               "10d9b7ad-3b8a-4425-8b41-ab69492a8880"
	            ]
	         },
	         "id":"aa4c4d4b-5b05-4ff8-a20a-238b86f270df",
	         "version":0,
	         "domainObjectId":"19151ca7-d755-4005-b76c-483309475c39",
	         "assignmentDate":"2017-07-25T16:25:33.383Z"
	      },
	      {  
	         "_metadata":{  
	            "correlation":{  
	               "client":"445ceccf-34bb-470f-a345-8be3f4e3ff2f"
	            },
	            "context":{  
	               "user":"a085e359-6069-4694-8820-000000000000"
	            },
	            "id":"d809e397-5e9e-4ccb-b479-cb716d4cfeb9",
	            "name":"public.resulting.case-to-be-resulted-assigned",
	            "causation":[  
	               "c925e708-c61b-4039-b1ea-0c33f233619a",
	               "4092ecc5-bb95-46b8-ba40-f8027dc872c4"
	            ]
	         },
	         "caseId":"19151ca7-d755-4005-b76c-483309475c39",
	         "assignedAt":"2017-07-25T16:25:33.203Z"
	      },
	      {  
	         "_metadata":{  
	            "correlation":{  
	               "client":"85d3af3a-e5de-47f2-a749-df277e4738d0"
	            },
	            "context":{  
	               "user":"a085e359-6069-4694-8820-000000000000"
	            },
	            "id":"ce7553f3-3567-4970-88df-ed160cfd1de5",
	            "name":"public.resulting.case-not-assigned",
	            "causation":[  
	               "e0114098-477e-4a43-be2e-faeaefb51383",
	               "09672b1b-13c8-47be-9aad-2bdffec3cc36"
	            ]
	         }
	      },
	      {  
	         "_metadata":{  
	            "correlation":{  
	               "client":"a53a5353-e167-4a78-9fb3-d368e3556618"
	            },
	            "context":{  
	               "user":"a085e359-6069-4694-8820-000000000000"
	            },
	            "id":"906d09ba-a7cd-4a06-ad77-ae5f5df576da",
	            "name":"public.resulting.referenced-decisions-saved"
	         },
	         "resultedOn":"2017-07-25T16:18:47.175Z",
	         "caseId":"ed43c50c-db14-44d5-80d8-dacb569e4572"
	      }
	   ]
	}

## Some tutorials
JsonPath [on github](https://github.com/json-path/JsonPath)

Quick [tutorial](http://goessner.net/articles/JsonPath/)



## JSONPath Online Evaluator

Test your json path expressions online with [jsonquerytool.com](http://www.jsonquerytool.com/)