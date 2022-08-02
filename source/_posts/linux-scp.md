---
title: 在linux之间传输文件的命令scp
date: 2022-08-02
categories:
  - Linux
tags: 
  - scp
---

> 作者：李旭光
> 引用请标明出处

## 前言
这里假设要从Linux服务器A中把文件 file.txt 传输到Linux服务器B，该怎么做，这里就可以用scp实现
为了能够成功的传输文件，需要做到以下几点：
1. A需要知道要传输的的文件本地的路径
2. A为了往B传输文件，所以需要知道B的访问权限，因此需要B的访问地址，账号和私钥
3. 目标路径
4. 因为Linux权限管理的要求，私钥的访问权限需要设定为600

当然还有一点前提是A和B两台服务器之间本身是可以通信的
知道以上信息便可以进行文件传输

## 实践
现在假设
1. A要往B传输的文件在A的如下目录：/tmp/transfer/file.txt
2. B的访问地址是 192.168.31.31 访问用户名是 testuser 用到的key上传到了A的如下目录： /tmp/ssh/test.key
3. 目标路径为B的如下目录： /tmp/test
知道上面信息后我们来创建命令。

```ssh
chomd 600 /tmp/ssh/test.key
scp -i /tmp/ssh/test.key /tmp/transfer/file.txt testuser@192.168.31.31 /tmp/test
```

通过上面的命令即可实现从A服务器传输文件到B服务器了。