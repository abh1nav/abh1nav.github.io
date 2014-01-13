---
layout: post
title: Simple logRotate script for MongoDB
date: '2012-08-17T10:34:50-04:00'
tags:
- log rotate
- log rotation
- mongodb
- nosql
- logging
- mongo
tumblr_url: http://abhinav.ca/post/29621952743/mongo-db-log-rotate
---
logRotate.js
{% codeblock lang:js %}
db.getMongo().getDB("admin").runCommand("logRotate")
{% endcodeblock %}
logRotate.sh
{% codeblock lang:sh %}
#!/bin/sh
# Clear old logs
rm /data/logs/mongod.log.*
# Rotate logs
mongo logRotate.js
{% endcodeblock %}