---
layout: post
title: "Redis Server pwning your disk IO?"
date: '2012-12-10T13:11:58-05:00'
tags:
- redis
- io
- wa
- defunct
- redis-server
tumblr_url: http://abhinav.ca/post/37647718445/redis-server-defunct-pwning-disk-io
---
Run as root:
{% codeblock lang:sh %}
echo 1 > /proc/sys/vm/overcommit_memory
{% endcodeblock %}
And restart your redis-server process.
