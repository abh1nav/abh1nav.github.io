---
layout: post
title: Deleting local and remote Git branches
date: '2012-10-19T14:09:43-04:00'
tags:
- git
- branch
- delete
- git remote
tumblr_url: http://abhinav.ca/post/33902743078/delete-local-remote-git-branches
---
Delete a local git branch:
{% codeblock lang:sh %}
git branch -D mybranch
{% endcodeblock %}
Delete a remote git branch:
{% codeblock lang:sh %}
git push remotename :mybranch
{% endcodeblock %}