---
layout: post
title: Delete all Objects in an S3 Bucket
date: '2012-08-03T15:25:00-04:00'
tags:
- s3
- aws
- amazon
- simple storage service
- delete bucket
- s3 bucket
- xargs
- awk
- s3cmd
- clear s3 bucket
tumblr_url: http://abhinav.ca/post/28644000691/delete-all-objects-in-s3-bucket
---
Using [s3cmd](http://s3tools.org/s3cmd).
{% codeblock lang:sh %}
s3cmd --recursive ls s3://bucketname | \
awk ''{print $4}'' | \
xargs s3cmd del
{% endcodeblock %}
