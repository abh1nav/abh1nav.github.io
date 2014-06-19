---
layout: post
title: "Produtionize your Docker dev environment"
date: 2014-06-19 10:31:08 -0400
comments: true
categories: 
published: false
---

## This is a draft

First, we'll need a script to do all the things we did manually, such as starting redis-server, invoking npm install etc. for when the container starts up in detached mode (this is normally how production containers should be run). I typically create a `run.sh` script next to the Dockerfile.

{% codeblock lang:bash run.sh %}
#!/bin/bash

echo Starting Redis
service redis-server start

cd /src

echo Cleaning up
rm -Rf node_modules

echo NPM Install
npm install -g forever --quiet
npm install --quiet

echo Starting server
forever server.js
{% endcodeblock %}

In the production script, we've switched from using `nodemon` to using `foreverjs` because we don't need a file watcher, just a process supervisor.

After making sure `run.sh` is executable, the next step is to modify the Dockerfile to tell it about the start script by appending the following to the end:

{% codeblock Dockerfile %}
CMD /src/run.sh
{% endcodeblock %}

##Level 2: Persisting Redis State Between Containers

```
RUN \
	cd /etc/redis && \
	mv redis.conf redis.conf.default && \
	ln -s /src/conf/redis.conf
```
  
This is a tricky bit- we move the default `/etc/redis/redis.conf` file to `/etc/redis/redis.conf.default`. At this point, if you try to start `redis-server` - it won't work because the config's missing. 
  
However, we then force a symlink in `/etc/redis/` to `/src/conf/redis.conf` - mind you, the `/src` directory doesn't exist yet, but more on that later.