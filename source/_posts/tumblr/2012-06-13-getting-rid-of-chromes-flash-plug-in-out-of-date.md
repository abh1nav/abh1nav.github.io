---
layout: post
title: Getting rid of Chrome's "flash plug-in out of date" message on Ubuntu 12.04
date: '2012-06-13T16:14:00-04:00'
tags:
- adobe
- chrome
- flash
- flashplayer
- google chrome
- linux
- ubuntu
- ubuntu 12.04
tumblr_url: http://abhinav.ca/post/25040552496/getting-rid-of-chromes-flash-plug-in-out-of-date
---
Type `about:plugins` in the address bar and click `Details` on the top right corner.  

Under `Flash`, look at all the `libflashplayer.so` files in use.  
  
Disable all except the actual chrome plugin listed under `/opt/google/chrome/plugins`.  

Check the version number of the Chrome flashplayer and update as necessary by downloading the latest package from adobe.com.