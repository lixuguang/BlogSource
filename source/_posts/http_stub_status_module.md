---
title: http_stub_status_module Nginx的性能模块
date: 2022-06-09 12:40:40
categories: 
  - [Web服务器, Nginx]
tags: 
  - Nginx
---

ngx_http_stub_status_module 是一个 Nginx 的内置 模块，可以提供 Nginx 的简单统计信息。默认情况下这个模块是不被编译进来的，所以在编译 Nginx 时要指定加载该模块：
```cmd
--with-http_stub_status_module
```
## 查看是否安装
```cmd
// 查看nginx版本及安装了那些模块
nginx -V

// 如果安装了的话会显示下面的信息
--with-http_stub_status_module
```

## 配置使用
nginx.conf配置文件中进行如下配置
```cmd
location /ngx_status {
            stub_status on; // 设置开启性能模块
        }
```
通过浏览器访问 http://localhost/ngx_status 就能看到你的nginx服务性能监控画面了
![image](https://user-images.githubusercontent.com/4262121/172983420-82b23fe2-eb5a-48c8-a637-4dfa2b846890.png)

## 解析
- 第一行：Active connections：活动连接数
- 第二三行：Server accepts handled requests：（三个数字分别代表）总共处理的连接数，成功握手的连接数量，处理的请求数（正常情况下握手和连接数是相等的，表示没有丢失）
- 第四行：
  - Reading: Nginx 读取到客户端的Header信息数
  - Writing: Nginx 返回给客户端的Header信息数
  - Waiting: 开启keep-alive的情况下,这个值等于 active – (reading + writing),意思就是Nginx已经处理完成,正在等候下一次请求指令的驻留连接（在nginx开启了keep-alive,也就是长连接的情况下，客户端跟服务端建立了连接但是没有读写操作的空闲状态）

## 使用场景
- 可以简单的用脚本做监控
- 可以用开源工具，zabbix，prometheus等去采集nginx的统计信息，做监控和历史数据采集
