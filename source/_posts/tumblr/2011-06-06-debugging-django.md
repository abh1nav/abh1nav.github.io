---
layout: post
title: Debugging Django
date: '2011-06-06T14:09:00-04:00'
tags: 
tumblr_url: http://abhinav.ca/post/6254292554/debugging-django
---
I was looking to accomplish a simple task - print debug messages in a Django View that would tell me what’s going on. Came across the following in a post:

{% blockquote Simon Willison http://bit.ly/jr3wE1 %}
You can configure it in your `settings.py`:
{% codeblock lang:python %}
import logging
logging.basicConfig(
    level = logging.DEBUG,
    format = ''%(asctime)s %(levelname)s %(message)s'',
)
{% endcodeblock %}
Then call it from any of your views:
{% codeblock lang:python %}
def my_view(request): 
    import logging 
    logging.debug("A log message")      
{% endcodeblock %} 

{% endblockquote %}

He then goes on to explain more advanced use cases using the Python Debugger, but this much was enough for what I was looking to do with it. 