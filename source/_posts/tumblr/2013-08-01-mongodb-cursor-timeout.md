---
layout: post
title: MongoDB Cursor Timeout
date: '2013-08-01T15:04:00-04:00'
tags:
- MongoDB
- java
- java driver
- wat
- bad defaults
- mongo
tumblr_url: http://abhinav.ca/post/57083677816/mongodb-cursor-timeout
---
When using the MongoDB Java Driver, if you have long running operations that require you to keep a cursor open, you’ll end up with a MongoException that says **“oops, the cursor timed out”** after about 10 minutes of activity.  

The Mongo docs say that cursors timeout due to inactivity, but as of driver version 2.11.1, I’ve had cursors timeout after 10 minutes even though documents were being fetched from the cursor continuously. Turns out, the fix to keep the cursor alive is surprisingly easy.
{% codeblock lang:java %}
cursor.addOption(com.mongodb.Bytes.QUERYOPTION_NOTIMEOUT);
{% endcodeblock %}

