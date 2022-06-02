---
title: docker 命令
date: 2022-06-02 16:20:30
categories: 
  - Docker
  - Infra
tags: 
  - Docker
---

## docker 命令

- 帮助命令
docker --help

### 镜像操作

- 搜索镜像
docker search hello-world

- 下载镜像
docker pull hello-world

- 查看本地已下载所有镜像
docker images

- 查看镜像历史
docker history hello-world

- 备份镜像
docker tag hello-word:last hello-world:v2

- 删除镜像
docker rmi hello-word:last

- 删除未使用过的镜像
docker image prune

- 导出镜像
docker save -o hello-world:last.tar hello-world:last

- 导入镜像
docker load -i hello-world:last.tar

- 查看镜像信息
docker image inspact nginx

### 容器操作
 
- 查看所有容器 [-q 编号] [-a active 启动的容器]
docker ps [-a] [-q]

- 启动容器 [-d 后台启动]
docker run -d --name nginx1 nginx:last

- 停止容器
docker stop nginx1

- 启动容器()
docker start nginx1

- 删除容器
docker rm nginx1

- 批量删除运行中容器
docker rm $(docker ps -q) -f

- 创建容器并进入
docker run -it --name nginx1 nginx:last /bin/bash

- 退出容器
exit

- 进入容器
docker exec -it nginx1 /bin/bash

- 通过容器创建镜像
docker commit -m 'laowang' nginx1 nginx:v1

- 查看容器信息
docker container inspact nginx1
