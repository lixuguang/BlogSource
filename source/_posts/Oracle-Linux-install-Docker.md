---
title: 在 Oracle Linux 中安装 Docker
date: 2022-08-02 18:00:00
categories: 
  - Linux
  - Oracle
  - Docker
tags: 
  - Docker
---

> 作者：李旭光
> 引用请标明出处

# Oracle Linux install Docker(https://docs.docker.com/engine/install/centos/)

```ssh
# 查看
systemctl list-units

sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install -y yum-utils

sudo yum -y install docker-ce-20.10.17 docker-ce-cli-20.10.17 containerd.io-1.6.6

sudo vi /etc/yum.repos.d/docker-ce.repo

[centos-extras]
name=Centos extras - $basearch
baseurl=http://mirror.centos.org/centos/7/extras/x86_64
enabled=1
gpgcheck=1
gpgkey=http://centos.org/keys/RPM-GPG-KEY-CentOS-7

sudo yum -y install fuse-overlayfs slirp4netns

# sudo chmod 777 docker-ce.repo

# yum list docker-ce --showduplicates | sort -r

sudo systemctl start docker

sudo docker run hello-world

sudo yum install /path/to/package.rpm
sudo systemctl start docker
sudo docker run hello-world
```