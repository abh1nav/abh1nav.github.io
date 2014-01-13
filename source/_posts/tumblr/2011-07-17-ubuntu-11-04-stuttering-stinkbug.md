---
layout: post
title: Ubuntu 11.04 - Stuttering Stinkbug
date: '2011-07-17T01:04:15-04:00'
tags:
- ubuntu
- stuttering
- mouse lag
- ubuntu mouse lag
- ubuntu stuttering
- '11.04'
- ubuntu 11.04
- ubuntu 10
- ubuntu 10.10
- maverick
- meerkat
- natty
- narwhal
- kworker
- kslowd
- kworker cpu
- top
tumblr_url: http://abhinav.ca/post/7713902897/ubuntu-11-04-stuttering-stinkbug
---
I’ve been living with an annoying bug in Ubuntu on my ThinkPad for quite a while now. If I recall correctly, it started off in Ubuntu 9.10 and has continued through to 11.04 as well. The main effect of this bug causes the mouse to stutter, even when the system is idle. This bug reported on launchpad describes the stuttering as a result of kslowd spawning multiple instances and pegging CPU time. This bug was prevalent throughout `2.6.35-*` versions of the Ubuntu kernels.  

In the `2.6.36-*` variation, kslowd was renamed to kworker and contrary to what others have reported, in my experience, the problem worsened. No fix was in sight yet.  
  
Even in today’s latest `2.6.38-*` series, many users have reported `kworker` processes pegging enormous amounts of CPU time, rendering the system useless for a few seconds ever so often.  

A fix that worked for me involves disabling the poll parameter of the `drm_kms_helper` kernel module. You can try out the fix by executing (as root, of course)
{% codeblock lang:sh %}
echo N > /sys/module/drm_kms_helper/parameters/poll
{% endcodeblock %}
If it works out well, you can make the fix permanent by adding the following line to your `/etc/modprobe.d/local.conf`
{% codeblock lang:sh %}
options drm_kms_helper poll=N
{% endcodeblock %}
Good luck!
