---
layout: post
title:  "Basic requests with curl"
date:   2016-04-25 21:09:24 +0100
categories: web-tools
tags: curl
---
_"Curl is a tool to transfer data from or to a server, using one of the supported protocols"._ For me it's sometimes the best solution to send requests from terminal. Especially that you can directly copy created curl request generated in developer tools in chrome or firefox.

The format:

	curl [options] [URL...]

Example of simple POST request with json payolad and some headers needed:
```bash
	curl -X POST -d "[{"organisationId": 203040,"type": "Change","dateCreated": 1459944707610}]"
	 -H "Content-type: application/json"
	 -H "Accept: application/json" "localhost:1234/your/url/here"
```
Check if you don't need to escape quotes from your data string e.g.:

	\"organisationId\": 20526,

## Options that I often use
_From [man page](https://curl.haxx.se/docs/manpage.html)_

	-d, --data <data>

Sends the specified data in a POST request to the HTTP server

	-H, --header <header>

Extra header to include in the request when sending HTTP to a server. 

	-X, --request <command>

Specifies a custom request method to use when communicating with the HTTP server. The specified request method will be used instead of the method otherwise used (which defaults to GET). Common additional HTTP requests include PUT and DELETE