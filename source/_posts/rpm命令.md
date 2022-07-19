---
title: rpm命令
date: 2020-07-19
categories: 
  - Linux
  - RPM
tags: 
  - RPM
---

> 作者：李旭光
> 引用请标明出处

## 安装
sudo rpm -ivh [lz4-1.8.3-1.el7.x86_64.rpm]

## 卸载
sudo rpm -e [postgresql14-server-14.4-1PGDG.rhel7.x86_64]

## 查询功能

命令格式 rpm {-q|--query} [select-options] [query-options]

　　RPM的查询功能是极为强大，是极为重要的功能之一；举几个常用的例子，更为详细的具体的，请参考#man rpm

1、对系统中已安装软件的查询

1）查询系统已安装的软件

　　语法：rpm -q 软件名

　　举例：[root@localhost beinan]# rpm -q gaim

　　gaim-1.3.0-1.fc4 　　

       查看系统中所有已经安装的包，要加 -a 参数 ；

　　[root@localhost RPMS]# rpm -qa

　　如果分页查看，再加一个管道 |和more命令；

　　[root@localhost RPMS]# rpm -qa |more

　　在所有已经安装的软件包中查找某个软件，比如说 gaim ；可以用 grep 抽取出来；

　　[root@localhost RPMS]# rpm -qa |grep gaim

　　上面这条的功能和 rpm -q gaim 输出的结果是一样的；

2）查询一个已经安装的文件属于哪个软件包

　　语法 rpm -qf 文件名

　　注：文件名所在的绝对路径要指出

　　举例：

　　[root@localhost RPMS]# rpm -qf /usr/lib/libacl.la

　　libacl-devel-2.2.23-8

3）查询已安装软件包都安装到何处

　　语法：rpm -ql 软件名 或 rpm rpmquery -ql 软件名

　　举例：

　　[root@localhost RPMS]# rpm -ql lynx

　　[root@localhost RPMS]# rpmquery -ql lynx

4）查询一个已安装软件包的信息

　　语法格式： rpm -qi 软件名

　　举例：

　　[root@localhost RPMS]# rpm -qi lynx

5）查看一下已安装软件的配置文件

　　语法格式：rpm -qc 软件名

　　举例：

　　[root@localhost RPMS]# rpm -qc lynx

6）查看一个已经安装软件的文档安装位置

　　语法格式： rpm -qd 软件名

　　举例：

　　[root@localhost RPMS]# rpm -qd lynx

7）查看一下已安装软件所依赖的软件包及文件

　　语法格式： rpm -qR 软件名

　　举例：

　　[root@localhost beinan]# rpm -qR rpm-python

　　查询已安装软件的总结：对于一个软件包已经安装，我们可以把一系列的参数组合起来用；比如 rpm -qil ；比如：

　　[root@localhost RPMS]# rpm -qil lynx

2、对于未安装的软件包的查看：

　　查看的前提是您有一个.rpm 的文件，也就是说对既有软件file.rpm的查看等；

1）查看一个软件包的用途、版本等信息；

　　语法： rpm -qpi file.rpm

　　举例：

　　[root@localhost RPMS]# rpm -qpi lynx-2.8.5-23.i386.rpm

2）查看一件软件包所包含的文件；

　　语法： rpm -qpl file.rpm

　　举例：

　　[root@localhost RPMS]# rpm -qpl lynx-2.8.5-23.i386.rpm

3）查看软件包的文档所在的位置；

　　语法： rpm -qpd file.rpm

　　举例：

　　[root@localhost RPMS]# rpm -qpd lynx-2.8.5-23.i386.rpm

4）查看一个软件包的配置文件；

　　语法： rpm -qpc file.rpm

　　举例：

　　[root@localhost RPMS]# rpm -qpc lynx-2.8.5-23.i386.rpm

5）查看一个软件包的依赖关系

　　语法： rpm -qpR file.rpm

　　举例：

　　[root@localhost archives]# rpm -qpR yumex_0.42-3.0.fc4_noarch.rpm

　　/bin/bash

　　/usr/bin/python

　　config(yumex) = 0.42-3.0.fc4

　　pygtk2

　　pygtk2-libglade

　　rpmlib(CompressedFileNames) <= 3.0.4-1

　　rpmlib(PayloadFilesHavePrefix) <= 4.0-1

　　usermode

　　yum >= 2.3.2