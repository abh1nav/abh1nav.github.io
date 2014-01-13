---
layout: post
title: 'Twitter API: 401 Unauthorized'
date: '2013-05-23T19:30:00-04:00'
tags: 
tumblr_url: http://abhinav.ca/post/51182927585/twitter-api-401-unauthorized
---
**Symptom**: Twitter API returns a 401 Unauthorized when you start the OAuth process by obtaining a bearer token. This can happen all of a sudden possibly breaking existing processes that were working.  
  
{% img /images/tumblr/2013-05-23-1.jpg 640 640 %}

**Problem**: The most likely culprit is the clock on your server. If this gets out of sync (even by as little as 20s), Twitter’s amazing API will barf and return a helpful _“401 Unauthorized”_
  
**Solution**:
{% codeblock lang:sh %}
sudo ntpdate ntp.ubuntu.com
{% endcodeblock %}
