---
layout: post
title: List all folders in descending order of size
date: '2012-10-17T23:11:00-04:00'
tags:
- awk
- disk usage
- du
- folder size
- linux
- shell
- unix
tumblr_url: http://abhinav.ca/post/33817065791/list-all-folders-in-descending-order-of-content-size
---
Fancy [`du`](http://linux.die.net/man/1/du) trick to list all folders in the current folder in descending order of size of content with human readable folder-sizes.

{% codeblock lang:sh %}
du -k -d 1 | sort -nr | awk ''
     BEGIN {
        split("KB,MB,GB,TB", Units, ",");
     }
     {
        u = 1;
        while ($1 >= 1024) {
           $1 = $1 / 1024;
           u += 1
        }
        $1 = sprintf("%.1f %s", $1, Units[u]);
        print $0;
     }
    ''
{% endcodeblock %}
