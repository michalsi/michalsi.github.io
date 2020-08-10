---
layout: post
title:  "Java script - working with csv encoding"
date:   2020-08-09 19:37:59 +0100
categories: coding
tags: java-script node
---

In Java Script CSV file can be parred with [csv-parser ](https://www.npmjs.com/package/csv-parser)

When using it to work with a file that contained polish characters I got problems weird output.

![Z??wno??]({{ site.url }}/assets/2020-08-09-missing-encoder.png "Wrong encoding")

Proper encoding was missing. In this case [ISO-8859-2]( https://en.wikipedia.org/wiki/ISO/IEC_8859-2)  

Csv-parser docs recommends to use [iconv-lite](https://www.npmjs.com/package/iconv-lite).

I just double checked supported encodings on the parent project [inconv](https://www.npmjs.com/package/iconv).
And added one line which solved my problems.

```
fs.createReadStream('my.csv')
       .pipe(iconv.decodeStream('ISO-8859-2'))
```