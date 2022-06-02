# 搭建Docker私有仓库

利用registry搭建私有仓库

## 下载registry

docker pull registry

## 配置

/etc/docker/doemon.json
配置 'insecure-registry'

## 重启docker
systemctl restart docker

## 创建registry容器（关联私有仓库配置）
docker run -d -p 5000:5000 --name registry registry:latest

## 推送镜像到私有仓
- 备份镜像(172.16.12.134:5000 私有仓地址)
docker tag ubuntu:latest 172.16.12.134:5000/my_ubuntu

- 推送
docker push 172.16.12.134:5000/my_ubuntu

- 下载
docker pull 172.16.12.134:5000/my_ubuntu

