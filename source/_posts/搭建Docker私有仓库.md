---
title: 搭建Docker私有仓库
date: 2022-08-02 18:00:00
categories: 
  - Docker
tags: 
  - registry
---

# 搭建Docker私有仓库

利用registry搭建私有仓库

## 下载registry
```ssh
docker pull registry
```

## 配置
```ssh
/etc/docker/doemon.json
# 配置 'insecure-registry'
```
## 重启docker
```ssh
systemctl restart docker
```

## 创建registry容器（关联私有仓库配置）
```ssh
docker run -d -p 5000:5000 --name registry registry:latest
```

## 推送镜像到私有仓
- 备份镜像(172.16.12.134:5000 私有仓地址)
```ssh
docker tag ubuntu:latest 172.16.12.134:5000/my_ubuntu
```

- 推送
```ssh
docker push 172.16.12.134:5000/my_ubuntu
```

- 下载
```ssh
docker pull 172.16.12.134:5000/my_ubuntu
```
