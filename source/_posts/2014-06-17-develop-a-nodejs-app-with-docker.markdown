---
layout: post
title: "Develop a NodeJS app with Docker"
date: 2014-06-17 01:58:40 -0400
comments: true
categories: 
published: false
---

This is a somewhat detailed tutorial on using Docker as a replacement for [Vagrant](http://www.vagrantup.com/) when developing a Node app using the [Express](http://expressjs.com/) framework. To make things a bit more representative of the real world, the app will persist session information in Redis using the [connect-redis](https://github.com/visionmedia/connect-redis) middleware.
  
###Step 1: The Node App
The app consists of a `package.json` and `server.js` which is about as simple as it gets.

{% codeblock lang:javascript package.json %}
{
    "name": "docker-dev",
    "version": "0.1.0",
    "description": "Docker Dev",
    "dependencies": {
        "connect-redis": "~1.4.5",
        "express": "~3.3.3",
        "hiredis": "~0.1.15",
        "redis": "~0.8.4"
    }
}
{% endcodeblock %}
  
{% codeblock lang:javascript server.js %}
var express = require('express'),
    app = express(),
    redis = require('redis'),
    RedisStore = require('connect-redis')(express),
    server = require('http').createServer(app);

app.configure(function() {
  app.use(express.cookieParser('keyboard-cat'));
  app.use(express.session({
        store: new RedisStore({
            host: process.env.REDIS_HOST || 'localhost',
            port: process.env.REDIS_PORT || 6379,
            db: process.env.REDIS_DB || 0
        }),
        cookie: {
            expires: false,
            maxAge: 30 * 24 * 60 * 60 * 1000
        }
    }));
});

app.get('/', function(req, res) {
  res.json({
    status: "ok"
  });
});

var port = process.env.HTTP_PORT || 3000;
server.listen(port);
console.log('Listening on port ' + port);
{% endcodeblock %}

`server.js` pulls in all the dependencies and starts an express app. The express app is configured to store session information in Redis and exposes a single endpoint that returns a status message as JSON. Pretty standard stuff.  
  
One thing to note here is that the connection information for redis can be overridden using environment variables - this will be useful later on when moving from dev to prod.  
  
###Step 2: The Dockerfile
To get this app running inside a docker container, it needs a Dockerfile.

{% codeblock Dockerfile %}
FROM dockerfile/ubuntu

MAINTAINER Abhinav Ajgaonkar <abhinav316@gmail.com>

# Install Redis
RUN	\
	apt-get -y -qq install python redis-server

# Install Node
RUN	\
	cd /opt && \
	wget http://nodejs.org/dist/v0.10.28/node-v0.10.28-linux-x64.tar.gz && \
	tar -xzf node-v0.10.28-linux-x64.tar.gz && \
	mv node-v0.10.28-linux-x64 node && \
	cd /usr/local/bin && \
	ln -s /opt/node/bin/* . && \
	rm -f /opt/node-v0.10.28-linux-x64.tar.gz

# Link the redis config
RUN \
	cd /etc/redis && \
	mv redis.conf redis.conf.default && \
	ln -s /src/conf/redis.conf

# Set the working directory
WORKDIR	/src
{% endcodeblock %}

Taking it line by line,

```
FROM dockerfile/ubuntu
``` 

This tells docker to use the `dockerfile/ubuntu` image provided by Docker Inc. as the base image for the build.

```
# Install Redis
RUN	\
	apt-get -y -qq install python redis-server
```

The base image contains absolutely nothing- so we need to specify everything needed for our app to run. This statement installs python and redis-server. Redis server is required because we'll be storing session info in it and python is required by npm to be able to build the C-extension used by the redis node module.
  
```
# Install Node
RUN	\
	cd /opt && \
	wget http://nodejs.org/dist/v0.10.28/node-v0.10.28-linux-x64.tar.gz && \
	tar -xzf node-v0.10.28-linux-x64.tar.gz && \
	mv node-v0.10.28-linux-x64 node && \
	cd /usr/local/bin && \
	ln -s /opt/node/bin/* . && \
	rm -f /opt/node-v0.10.28-linux-x64.tar.gz
```
  
This downloads the 64-bit NodeJS binaries and extracts them to `/opt` while creating symlinks in `/usr/local/bin/` to make the `node` and `npm` commands available on the `PATH`.
  
```
# Link the redis config
RUN \
	cd /etc/redis && \
	mv redis.conf redis.conf.default && \
	ln -s /src/conf/redis.conf
```
  
This is a tricky bit- we move the default `/etc/redis/redis.conf` file to `/etc/redis/redis.conf.default`. At this point, if you try to start `redis-server` - it won't work because the config's missing. 
  
However, we then force a symlink in `/etc/redis/` to `/src/conf/redis.conf` - mind you, the `/src` directory doesn't exist yet, but more on that later.
  
```
WORKDIR	/src
```
  
This tells docker to `cd /src` once the container has started.

###Step 3: Build and run the container

Now that the docker file is in 

