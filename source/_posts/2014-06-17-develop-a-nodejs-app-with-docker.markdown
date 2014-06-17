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
For development, we'll have redis and node running in the same container. To make this happen, we'll use a Dockerfile to configure the container.

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

# Set the working directory
WORKDIR	/src
{% endcodeblock %}

Taking it line by line,

```
FROM dockerfile/ubuntu
``` 

This tells docker to use the `dockerfile/ubuntu` image provided by Docker Inc. as the base image for the build.

```
RUN	\
	apt-get -y -qq install python redis-server
```

The base image contains absolutely nothing- so we need to apt-get everything needed for our app to run. This statement installs python and redis-server. Redis server is required because we'll be storing session info in it and python is required by npm to be able to build the C-extension used by the redis node module.
  
```
RUN	\
	cd /opt && \
	wget http://nodejs.org/dist/v0.10.28/node-v0.10.28-linux-x64.tar.gz && \
	tar -xzf node-v0.10.28-linux-x64.tar.gz && \
	mv node-v0.10.28-linux-x64 node && \
	cd /usr/local/bin && \
	ln -s /opt/node/bin/* . && \
	rm -f /opt/node-v0.10.28-linux-x64.tar.gz
```
  
This downloads and extracts the 64-bit NodeJS binaries.
  
```
WORKDIR	/src
```
  
This tells docker to `cd /src` once the container has started.

###Step 3: Build and run the container

Now that the docker file is written, let's build a Docker image.
  
```
docker build -t sqldump/docker-dev:0.1 .
```
  
Once the image done building, we can launch a container using:

```
docker run -i -t --rm \
           -p 3000:3000 \
           -v `pwd`:/src \
           sqldump/docker-dev:0.1 \
           /bin/bash
```
  
Let's see what's going on in the docker run command.
  
`-i` starts the container in interactive mode (versus -d for detached mode). This means the container will exit once the interactive sessions is over.
  
`-t` allocates a pseudo-tty.
  
`--rm` removes the container and its filesystem on exit.
  
`-p 3000:3000` forwards port 3000 on to the host to port 3000 on the container.
  
`-v {% raw %}`pwd`{% endraw %}:/src` mounts the current working directory in the host (i.e. our project files) to `/src` inside the container. We mount the current folder as a volume rather than using the `ADD` command in the Dockerfile so that any changes we make to the files in a text editor will be seen by the container right away.

`sqldump/docker-dev:0.1` the name and version of the docker image to run - this is the same one we used when building the docker image.

`/bin/bash` is the command that's executed once the container has started. In development, we want it to drop into a bash shell.
  
If the docker run command succeeds, it'll drop you into something that looks like this:

[{% img /images/2014-06-17-Docker-Run.png 482 139 %}](/images/2014-06-17-Docker-Run.png)
  
###Step 4: Start Developing

Now that the container is running, we'll need to get a few standard, non-docker related things sorted out before we can start writing code. First, start redis server inside the container using:

```
service redis-server start
```

Second, install project dependencies:

```
npm install
npm install -g nodemon
```

Nodemon will be used to watch for changes in your JS files and restart the server as needed. 

Third, start up the server using:

```
nodemon server.js
```

Now, if you got `http://localhost:3000` in your browser, you should see something like this:

[{% img /images/2014-06-17-First-Run.png 230 110 %}](/images/2014-06-17-First-Run.png)

Let's add another endpoint to `server.js`:

{% codeblock lang:javascript server.js %}
app.get('/hello/:name', function(req, res) {
  res.json({
    hello: req.params.name
  });
});
{% endcodeblock %}

You should see that nodemon inside the container has detected your changes and restarted the server:

[{% img /images/2014-06-17-Reload.png 1072 390 %}](/images/2014-06-17-Reload.png)

And now, if you point your browser to `http://localhost:3000/hello/world`, you should see the response:

[{% img /images/2014-06-17-Second-Run.png 348 140 %}](/images/2014-06-17-Second-Run.png)

###Step 5: Production

When pushing the app to production, we'll need a script to do all the things we did manually, such as starting redis-server, invoking npm install etc. I typically create a `run.sh` script next to the Dockerfile.

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
RUN /src/run.sh
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
