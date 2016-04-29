---
layout: post
title:  "Mean docker"
date:   2016-02-01 10:10:21 +0000
categories: docker mongodb nodejs angular mean stack
description: "Mean stack docker"
published: false
tags:
- docker
- mean stack
- nodejs
---



### how to run something nice

Let's say we forgot how to create a _Dockerfile_ we can always pick one up, ready to go, from [Node Dockerfile][Node Dockerfile] if we fancy _Nodejs_.

#### Do some docker cleanup

List all your docker processes through ```docker ps -a```, if you want only the stopped one add ```-q```.

A quick way of removing multiple containers is by using rm followed by the container ids of interest

```
# the strings are a set of Container Ids
docker rm a77c136865d0 cb804b99fdb2 e90c23c6c302
```

# create a new container
docker build -t kishin/mean:1.0.0 .
# once created
docker run -a stdin -a stdout -i -t kishin/mean:1.0.0 /bin/bash


[Smashingmagazine's mean docker setup]: https://www.smashingmagazine.com/2016/04/stop-installing-your-webdev-environment-locally-with-docker/
