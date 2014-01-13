---
layout: post
title: pip install scipy on Ubuntu
date: '2013-09-19T01:17:29-04:00'
tags:
- python
- pip
- ubuntu
- dependency
- derp
- scipy
- numpy
- libfortran
- fortran
tumblr_url: http://abhinav.ca/post/61652028019/pip-install-scipy-on-ubuntu
---
On Ubuntu 12.04 Server, a pip install scipy barfs with
{% codeblock %}
library dfftpack has Fortran sources but no Fortran compiler found
{% endcodeblock %}
because it wants you to
{% codeblock lang:sh %}
sudo apt-get install libamd2.2.0 libblas3gf libc6 libgcc1 \
libgfortran3 liblapack3gf libumfpack5.4.0 libstdc++6 \
build-essential gfortran python-all-dev \
libatlas-base-dev
{% endcodeblock %}
and,
{% codeblock lang:sh %}
pip install numpy
{% endcodeblock %}
before you try to
{% codeblock lang:sh %}
pip install scipy
{% endcodeblock %}
