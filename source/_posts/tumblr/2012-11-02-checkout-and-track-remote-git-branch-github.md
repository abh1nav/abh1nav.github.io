---
layout: post
title: Check out and track remote branch from GitHub
date: '2012-11-02T02:05:31-04:00'
tags:
- git
- github
- remote
- checkout
- scm
- branch
- git branch
- git remote
tumblr_url: http://abhinav.ca/post/34816244140/checkout-and-track-remote-git-branch-github
---
For those times when you need to checkout a branch locally and set it up to track a remote branch that already exists on GitHub:
{% codeblock lang:sh %}
git checkout -t origin/myremotebranch
{% endcodeblock %}
