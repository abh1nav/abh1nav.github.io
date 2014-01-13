---
layout: post
title: Setup Ruby (2.x) on Rails (4.x) in 10 lines or less
date: '2013-12-01T01:38:00-05:00'
tags:
- ruby
- rails
- ops
- devops
- setup
- shell script
tumblr_url: http://abhinav.ca/post/68645318685/setup-ruby-2-x-on-rails-4-x-in-10-lines-or-less
---
As a first time user of Ruby and Rails, I was able to setup a sane environment on Ubuntu Server 12.04 (x64) in less than 10 commands:
{% codeblock lang:sh %}
cd $HOME
curl -L https://get.rvm.io | bash
source $HOME/.rvm/scripts/rvm
rvm requirements
rvm install ruby
rvm use ruby --default
rvm rubygems current
gem install rails
{% endcodeblock %}
And, if you already have a rails app to run, continue along with:
{% codeblock lang:sh %}
cd myapp
bundle install
rails server
{% endcodeblock %}
This will get you up and running with a dev server.
