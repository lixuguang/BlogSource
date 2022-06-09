---
title: Docker网络模式
date: 2022-06-02 11:23:20
categories: 
  - Infra
  - Docker
tags: 
  - 网络模式
---

# Docker网络模式
Docker常见的两种网络模式
- host网络模式：创建的容器和宿主机***共享同一个网卡***
- bridge网络模式：桥接模式，只要使用桥接模式创建容器网段一样（类似于172.16.1.1，172.16.1.2）有点像是子局域网

## 利用network命令管理网络模式
- 查看网络模式
docker network ls
- 创建网络模式
docker network create --drive bridge bridge_test
- 通过network断网
docker network disconnet bridge nginx5
- 通过network联网
docker network connect bridge nginx5
- 删除网络模式
docker network rm bridge_test
