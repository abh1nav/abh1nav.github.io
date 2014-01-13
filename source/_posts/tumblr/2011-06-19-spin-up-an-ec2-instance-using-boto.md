---
layout: post
title: Spin up an EC2 instance using boto
date: '2011-06-19T17:20:00-04:00'
tags:
- EC2
- Amazon
- AWS
- boto
- python
- API
tumblr_url: http://abhinav.ca/post/6700282173/spin-up-an-ec2-instance-using-boto
---
Boto provides an amazing interface to AWS APIs. Here is a simple example to spin up a `t1.micro` sized EC2 instance of the default Amazon Linux 64 bit AMI.
{% codeblock lang:python %}
from boto.ec2.connection import EC2Connection 
key = 'aws access key'
secret = 'aws secret access key'
connection = EC2Connection(
				aws_access_key_id=key, 
				aws_secret_access_key=secret)
ami = 'ami-8e1fece7' # AMI ID of the Amazon Linux x86_64 AMI 
reservation = c.run_instances(ami, 
				min_count=1, 
				max_count=1, 
				key_name='MyKeyPairName', 
				instance_type='t1.micro') 
instance = reservation.instances[0] 
while instance.update() != 'running':
	sleep(5) 
{% endcodeblock %}
`min_count` and `max_count` determine how many instances are to be started. By default, Amazon limits you to starting a maximum of 20 instances. If you need to start more, they require you to contact them with your use case.
