---
layout: post
title: Making OS X's Preview and Quicktime less annoying
date: '2012-07-19T15:46:44-04:00'
tags:
- apple
- os x
- preview
- quicktime
- annoying
- lion
- defaults
- geniuses
tumblr_url: http://abhinav.ca/post/27572291158/making-os-xs-preview-and-quicktime-less-annoying
---
Product discussion at Apple:

"Guys, we need 250 new features for the next release. What do you have for me?"  

"Here’s something: let’s open up ALL the files from the user’s previous session when the user decides to launch apps like Preview or Quicktime”  

"Why Bob, that’s a brilliant idea!"  

"Improving on what Bob said, let’s NOT give them the option to turn this behaviour off!”  

"Alright, let’s make it happen. 1 down, 249 to go."  

Run the following in your Terminal to prevent Preview (and/or Quicktime) from opening up all the windows from your previous session:
{% codeblock lang:sh %}
defaults write com.apple.Preview \
NSQuitAlwaysKeepsWindows -bool false
defaults write com.apple.QuickTimePlayerX \
NSQuitAlwaysKeepsWindows -bool false
{% endcodeblock %}
