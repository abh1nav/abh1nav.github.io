---
layout: post
title: Those @!#$ weird characters in Hadoop / Faunus output
date: '2013-08-07T14:40:00-04:00'
tags:
- faunus
- graph analytics
- hadoop
- python
- scrub
- unicode
- invisible character
tumblr_url: http://abhinav.ca/post/57629487705/those-weird-characters-in-hadoop-faunus-output
---
Text-file output from Faunus often always contains garbage characters. To scrub them out, I use this little python script:
{% codeblock lang:python %}
import re
from string import printable

f = open("output.csv", "r")
line = f.readline()
line = re.sub("[^{}]+".format(printable), "", line)
line = line.replace("\n", "")

while line:
  print line
  line = f.readline()
  if line:
    line = re.sub("[^{}]+".format(printable), "", line)
    line = line.replace("\n", "")

f.close()
{% endcodeblock %}

And then a simple
{% codeblock lang:sh %}
python process.py > scrubbed.output.txt
{% endcodeblock %}
