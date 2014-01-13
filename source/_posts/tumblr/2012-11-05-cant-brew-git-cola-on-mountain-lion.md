---
layout: post
title: Can't brew git-cola on Mountain Lion?
date: '2012-11-05T16:25:00-05:00'
tags:
- python
- git
- git-cola
- pyqt4
- homebrew
- mountain lion
- brew
- os x
tumblr_url: http://abhinav.ca/post/35076544741/cant-brew-git-cola-on-mountain-lion
---
You need to
{% codeblock lang:sh %}
export PYTHONPATH=/usr/local/lib/python2.7/site-packages:$PYTHONPATH
{% endcodeblock %}
