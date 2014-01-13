---
layout: post
title: mod_python on Amazon Linux
date: '2011-06-23T18:56:00-04:00'
tags: 
tumblr_url: http://abhinav.ca/post/6842826742/mod-python-on-amazon-linux
---
It takes a few commands to get mod_python with Apache up and running on the free tier Amazon Linux AMI of EC2.  
  
**Step 1**  
Install `Apache` and `mod_python` using
{% codeblock lang:sh %}
yum install httpd mod_python
{% endcodeblock %}
**Step 2**  
Add python handlers by adding the following lines to `/etc/httpd/conf.d/python.conf`
{% codeblock %}
LoadModule python_module modules/mod_python.so

<Directory "/var/www/html">
	Options Indexes FollowSymlinks MultiViews
	AllowOverride None
	Order allow,deny
	allow from all
	AddHandler mod_python .py
	PythonHandler mod_python.publisher
	PythonDebug On
</Directory>
{% endcodeblock %}
**Step 3**  
Add a script to serve by creating a file `/var/www/html/my.py` and add the following code to it:
{% codeblock lang:python %}
def index(req):
    return "Hello World!"
{% endcodeblock %}
Access the file using http://my-ec2-address/my.py (make sure port 80 is open on the EC2-firewall)
