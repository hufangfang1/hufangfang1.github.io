---
layout: post
title: Docker 容器镜像删除
categories: Docker
description: Docker 容器镜像删除
keywords: Linux, PHP, Docker
---

## 1、停止所有的container，这样才能够删除其中的images：

`docker stop $(docker ps -a -q)`

如果想要删除所有container的话再加一个指令：

`docker rm $(docker ps -a -q)`

## 2、查看当前有些什么images
`docker images`

## 3、删除images，通过image的id来指定删除谁

`docker rmi <image id>`

想要删除untagged images，也就是那些id为<None>的image的话可以用

`docker rmi $(docker images | grep "^<none>" | awk "{print $3}")`

要删除全部image的话

`docker rmi $(docker images -q)`

## 4、查看容器详细信息

`docker inspect (容器id)`

包含有网络、数据卷、当前状态、配置信息等
