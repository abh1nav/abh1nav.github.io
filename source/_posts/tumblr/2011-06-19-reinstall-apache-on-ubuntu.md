---
layout: post
title: Reinstall Apache on Ubuntu
date: '2011-06-19T22:47:12-04:00'
tags:
- apache
- reinstall
- ubuntu
- reset
- default
- apache2
tumblr_url: http://abhinav.ca/post/6710229140/reinstall-apache-on-ubuntu
---
This handy little shell script will reset your entire apache installation to what it was like when you first installed it.
{% codeblock lang:sh %}
APACHE_PKGS=`sudo dpkg --get-selections | grep apache | cut -f 1` 
echo $APACHE_PKGS 
sudo apt-get remove --purge $APACHE_PKGS 
sudo apt-get install $APACHE_PKGS
{% endcodeblock %}
Worked on Ubuntu 11.04. Source: http://bit.ly/mR0bOq
