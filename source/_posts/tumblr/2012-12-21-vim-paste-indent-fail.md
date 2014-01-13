---
layout: post
title: Vim won't let you paste properly?
date: '2012-12-21T00:45:03-05:00'
tags:
- vim
- paste
- auto-indent
- fail
tumblr_url: http://abhinav.ca/post/38446223101/vim-paste-auto-indent-fail
---
When you paste in vim, if the indents look funny, then before you paste, run:
{% codeblock lang:vim %}
:set paste
{% endcodeblock %}
make sure you're in insert mode, do paste, then
{% codeblock lang:vim %}
:set nopaste
{% endcodeblock %}
and continue editing.