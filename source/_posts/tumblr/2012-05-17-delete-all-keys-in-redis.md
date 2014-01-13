---
layout: post
title: Delete all keys in Redis
date: '2012-05-17T13:10:25-04:00'
tags: 
tumblr_url: http://abhinav.ca/post/23234949406/delete-all-keys-in-redis
---
The **fun** way:
{% codeblock lang:sh %}
redis-cli KEYS ‘*’ | awk ‘{print $1}’ | xargs redis-cli DEL
{% endcodeblock %}
The **recommended** way:
{% codeblock lang:sh %}
redis-cli FLUSHALL
{% endcodeblock %}
