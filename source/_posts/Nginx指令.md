---
title: Nginx指令
date: 2022-06-09 10:40:40
categories: 
  - Web服务器
tags: 
  - Nginx
---

> 作者：李旭光
> 引用请标明出处

# Nginx指令

```cmd
#启动Nginx
start nginx

#重启Nginx
nginx -s reopen 

#重新加载Nginx配置文件，然后以优雅的方式重启Nginx
nginx -s reload

#强制停止Nginx服务
nginx -s stop

#优雅地停止Nginx服务（即处理完所有请求后再停止服务）
nginx -s quit

#检测配置文件是否有语法错误，然后退出
nginx -t 

#显示版本信息并退出
nginx -v 

#显示版本和配置选项信息，然后退出
nginx -V 

#检测配置文件是否有语法错误，然后退出
nginx -t 

#检测配置文件是否有语法错误，转储并退出
nginx -T 

#在检测配置文件期间屏蔽非错误信息
nginx -q 

#打开帮助信息
nginx -?,-h 

#设置前缀路径(默认是:/usr/share/nginx/)
nginx -p prefix 

#设置配置文件(默认是:/etc/nginx/nginx.conf)
nginx -c filename 

#设置配置文件外的全局指令
nginx -g directives 
```
