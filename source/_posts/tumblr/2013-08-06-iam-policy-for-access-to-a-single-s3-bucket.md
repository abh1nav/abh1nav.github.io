---
layout: post
title: IAM Policy for access to a Single S3 Bucket
date: '2013-08-06T10:54:00-04:00'
tags:
- IAM
- S3
- Bucket Policy
- AWS
- Security
tumblr_url: http://abhinav.ca/post/57522078230/iam-policy-for-access-to-a-single-s3-bucket
---
Assuming the bucket name is **my-bucket**
{% codeblock %}
{
  "Statement": [
    {
      "Action": [
        "s3:ListAllMyBuckets"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::*"
    },
    {
      "Action": "s3:*",
      "Effect": "Allow",
      "Resource": [
        "arn:aws:s3:::my-bucket", 
        "arn:aws:s3:::my-bucket/*"
      ]
    }
  ]
}
{% endcodeblock %}

Source: http://andrewhitchcock.org/?post=325
