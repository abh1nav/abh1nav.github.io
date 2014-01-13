---
layout: post
title: 'Homebrew and Python-MySQL '
date: '2013-08-13T17:05:12-04:00'
tags:
- mysql
- python
- python-mysqldb
- homebrew
- os x
tumblr_url: http://abhinav.ca/post/58179089933/homebrew-and-python-mysql
---
To be able to pip install the python mysql library on OS X, you need mysql client installed locally. If you have no need for the full mysql package, here’s how to get it working:
{% codeblock lang:sh %}
brew install mysql --client-only --universal
pip install MySQL-python
{% endcodeblock %}
