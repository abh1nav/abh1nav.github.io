---
layout: post
title: Git, Tags and GitHub
date: '2012-12-06T05:06:47-05:00'
tags:
- git
- github
- tags
- git-scm
- add tags
- remove tags
tumblr_url: http://abhinav.ca/post/37325350422/git-tags-github
---
Add a tag: 
{% codeblock lang:sh %}
git tag -a v0.1 -m "Version 0.1 Stable"
{% endcodeblock %}
Push tags to GitHub: 
{% codeblock lang:sh %}
git push origin --tags
{% endcodeblock %}
Delete tag locally:  
{% codeblock lang:sh %}
git tag -d v0.1
{% endcodeblock %}
Push to GitHub: 
{% codeblock lang:sh %}
git push origin --tags
{% endcodeblock %}
  
Rinse.  
  
Repeat.  
