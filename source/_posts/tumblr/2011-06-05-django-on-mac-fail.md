---
layout: post
title: Django on Mac = FAIL
date: '2011-06-05T01:02:57-04:00'
tags:
- compile mysqldb
- python-mysqldb
- django on mac
- mac
- macports
- MySQKdb on Mac
tumblr_url: http://abhinav.ca/post/6202477103/django-on-mac-fail
---

Django is awesome. Python is awesome. Mac OS is sort-of ok. So is MySQL. But geez, getting Python’s MySQL connector (MySQLdb) to play nice on the Mac OS made me start to lose my hair. Django needs this to be able to reach a MySQL server (which I wanted to use) to be able seamlessly run syncdb etc.  
  
There are varied sets of sparse instructions all over the interwebs on how to get MySQL db to compile for Mac OS; some ask you to edit source files before compiling, some are out of date and nearly all of them just don’t work.  

But... it’s a Mac... doesn’t it just work?! Apparently not. `</end rant>`  
  
The solution, this time, came to me in the form of MacPorts. MacPorts is a brilliant system that is the closest replacement to aptitude, for someone coming from a more Ubuntu-esque background (and I guess its existence makes sense, what with FreeBSD ports and all). A few simple lines to get you going on this:  
{% codeblock lang:sh %}
# install django and dependencies
sudo port install py26-django
# set py26 as default
sudo port select --set python python26
sudo port install py26-mysql
{% endcodeblock %}
Just to make sure these worked:
{% codeblock %}
macbookpro:~ asdf$ python
Python 2.6.6 (r266:84292, May 26 2011, 01:52:07) 
[GCC 4.2.1 (Apple Inc. build 5664)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import MySQLdb
>>> MySQLdb.version_info
(1, 2, 3, ''final'', 0)
>>>
{% endcodeblock %}