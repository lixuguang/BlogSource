---
title: Docker数据卷
date: 2020-06-02 11:21:20
categories: 
  - Docker
  - Infra
tags: 
  - 数据卷
---
# Docker数据卷
宿主机与容器进行数据交互，共享宿主机与容器之间的数据

## 创建数据卷关联
docker run -it --name my_ubuntu1 -v ~ /Desktop/test:/test /bin/bash 

## 利用系统方法操作数据卷
- 查 docker数据卷
docker volume ls

- 创建数据卷
docker volume create volname

- 共享
docker run -it --name my_ubuntu2 -v volname:/home ubuntu:latest /bin/bash

## 数据卷容器使用
可以通过数据卷容器，创建新的容器，并将多个容器绑定在一起

docker run -itd --name my_ubuntu3 --volumes-from my_ubuntu2 ubuntu:latest