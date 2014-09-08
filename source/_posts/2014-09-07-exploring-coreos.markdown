---
layout: post
title: "Exploring CoreOS"
date: 2014-09-07 04:21:47 -0400
comments: true
categories: coreos linux etcd systemd fleet
published: false
---

CoreOS is all the rage in Ops-land these days, so I thought I'd take some time to understand what all the fuss is about. Fundamentally, the CoreOS value proposition consists three components.   
- CoreOS: a minimal auto-updating clustered linux distro with built-in support for Docker  
- Etcd: a ditributed, zookeeper-like consistent key value store bundled with CoreOS  
- Fleet: a system to deploy and distribute your Docker containers keep running in the cluster  
  
## CoreOS
The distro is a stripped down version of Gentoo that doesn't even include a package manager. The CoreOS team believes that all applications should be deployed as containers (Docker or otherwise) in order to ensure a clean separation between things that don't change often (the kernel, systemd, ssh) and things that may change per application (python version, openssl version, etc). I wholeheartedly agree with this position and I think it will bump 'repeatable deployment' from the 'nice to have' into the 'must have' category, which is a good thing.

## Configuration & Clustering

### Why Cloud-Config is a bad idea

### Digital Ocean

### VirtualBox

### Discovery Issues

### Updates and Reboots
