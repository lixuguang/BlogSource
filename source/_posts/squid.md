---
title: Squid 服务器学习笔记
date: 2022-06-01
categories: 
  - Infra
tags: 
  - Squid
---

> 作者：李旭光
> 引用请标明出处


# Squid 服务器学习笔记
## Squid 服务器介绍
用来缓存前端资源的代理服务器，可以类比后端数据库缓存软件Redis。
当用户访问目标资源时由Squid服务器进行判断如果有数据缓存直接将缓存数据返回，如果没有缓存，代理客户访问资源，获取到后返回给用户，当有其他用户访问相同资源时直接返回资源。

## 代理方式
1. 普通代理
2. 透明代理
3. 反向代理

## 安装
``` script
// 检查是否已经安装
rpm -qa | grep squid
// 安装软件
rpm install squid
// 设置开机启动
chkconfig squid on
// 启动服务
service squid start
// 查看服务状态（端口监听3128）
netstat -anput |grap squid
// 关闭服务
service squid stop
```

## 配置文件
/etc/squid/squid.config

10.0.0.0/8

> 扩展知识
CIDR - 无类域间路由CIDR（Classless InterDomain Routing）
ip每位占1个字节8位，00000000 ~ 11111111，也就是0 ~ 255，ip共有四个位数。
10.0.0.0/8 的意思是 8位不可变，剩下的可变，8位这里的意思是说第一个IP段，那么也就是说IP范围是10.0.0.0 ~ 10.255.255.255 这样的一个ip范围。

// 复制并重命名
mv /etc/squid/squid.config{,.bak}
// 删除文件中的注释和空行（只保留有效设定）
awk 'if($0!~ /^#/ && $0~ !/^$/print $0)' /etc/squid/squid.config.bak > /etc/squid/squid.config

squid常用命令：
/usr/local/squid/sbin/squid -z 初始化缓存空间
/usr/local/squid/sbin/squid 启动
/usr/local/squid/sbin/squid -k shutdown 停止
/usr/local/squid/sbin/squid -k reconfigure 重新载入配置文件
/usr/local/squid/sbin/squid -k rotate 轮循日志

``` ini
#acl all src 0.0.0.0/0.0.0.0 and http_access allow all选项定义了一个访问控制列表。详细情况参见和Squid软件
#携带的文档。这里的访问控制列表允许所有对代理服务的访问，因为这里该代理是加速web服务器。
acl all src 0.0.0.0/0.0.0.0 
               #允许所有IP访问
acl manager proto http	#manager url协议为http
acl localhost src 127.0.0.1/255.255.255.255	#允午本机IP
acl to_localhost dst 127.0.0.1	#允午目的地址为本机IP
acl Safe_ports port 80	# 允许安全更新的端口为80
acl CONNECT method CONNECT	#请求方法以CONNECT
http_access allow all	#允许所有人使用该代理.因为这里是代理加速web服务器
http_reply_access allow all	#允许所有客户端使用该代理

acl OverConnLimit maxconn 16	#限制每个IP最大允许16个连接，防止攻击
http_access deny OverConnLimit

icp_access deny all	#禁止从邻居服务器缓冲内发送和接收ICP请求.
miss_access allow all	#允许直接更新请求
ident_lookup_access deny all	#禁止lookup检查DNS
http_port 8080 transparent	#指定Squid监听浏览器客户请求的端口号。

hierarchy_stoplist cgi-bin ?	#用来强制某些特定的对象不被缓存，主要是处于安全的目的。
acl QUERY urlpath_regex cgi-bin \?
cache deny QUERY

cache_mem 1 GB	#这是一个优化选项，增加该内存值有利于缓存。应该注意的是：
				#一般来说如果系统有内存，设置该值为(n/)3M。现在是3G 所以这里1G
fqdncache_size 1024	#FQDN 高速缓存大小
maximum_object_size_in_memory 2 MB	#允许最大的文件载入内存

memory_replacement_policy heap LFUDA	#动态使用最小的，移出内存cache
cache_replacement_policy heap LFUDA	#动态使用最小的，移出硬盘cache

cache_dir ufs /home/cache 5000 32 512	#高速缓存目录 ufs 类型使用的缓冲值最大允午1000MB空间，
										#32个一级目录，512个二级目录

max_open_disk_fds 0	#允许最大打开文件数量,0 无限制
minimum_object_size 1 KB	#允午最小文件请求体大小
maximum_object_size 20 MB	#允午最大文件请求体大小

cache_swap_low 90	#最小允许使用swap 90%
cache_swap_high 95	#最多允许使用swap 95%

ipcache_size 2048	# IP 地址高速缓存大小 2M
ipcache_low 90	#最小允许ipcache使用swap 90%
ipcache_high 95	#最大允许ipcache使用swap 90%


access_log /var/log/squid/access.log squid	#定义日志存放记录
cache_log /var/log/squid/cache.log squid
cache_store_log none	#禁止store日志

emulate_httpd_log on	#将使Squid仿照Web服务器的格式创建访问记录。如果希望使用
						#Web访问记录分析程序，就需要设置这个参数。

refresh_pattern . 0 20% 4320 override-expire override-lastmod reload-into-ims ignore-reload   #更新cache规则

acl buggy_server url_regex ^http://.... http://	#只允许http的请求
broken_posts allow buggy_server

acl apache rep_header Server ^Apache	#允许apache的编码
broken_vary_encoding allow apache

request_entities off	#禁止非http的标分准请求，防止攻击
header_access header allow all	#允许所有的http报头
relaxed_header_parser on	#不严格分析http报头.
client_lifetime 120 minute	#最大客户连接时间 120分钟

cache_mgr sky@test.com	#指定当缓冲出现问题时向缓冲管理者发送告警信息的地址信息。

cache_effective_user squid	#这里以用户squid的身份Squid服务器
cache_effective_group squid

icp_port 0	#指定Squid从邻居服务器缓冲内发送和接收ICP请求的端口号。
			#这里设置为0是因为这里配置Squid为内部Web服务器的加速器，
			#所以不需要使用邻居服务器的缓冲。0是禁用

cache_peer 127.0.0.1 parent 80 0 no-query default multicast-responder no-netdb-exchange #cache_peer 设置允许更新缓存的主机，因是本机所以127.0.0.1
cache_peer_domain 127.0.0.1                                 
hostname_aliases 127.0.0.1

error_directory /usr/share/squid/errors/Simplify_Chinese	#定义错误路径

always_direct allow all	# cache丢失或不存在是允许所有请求直接转发到原始服务器
ignore_unknown_nameservers on	#开反DNS查询，当域名地址不相同时候，禁止访问
coredump_dir  /var/log/squid	#定义dump的目录

max_filedesc 2048	#最大打开的文件描述

half_closed_clients off	#使Squid在当read不再返回数据时立即关闭客户端的连接。
						#有时read不再返回数据是由于某些客户关闭TCP的发送数据
						#而仍然保持接收数据。而Squid分辨不出TCP半关闭和完全关闭。

buffered_logs on	#若打开选项“buffered_logs”可以稍稍提高加速某些对日志文件的写入，该选项主要是实现优化特性。

acl tianya referer_regex -i tianya #防止天涯盗链，转嫁给百度
http_access deny tianya
deny_info  tianya

acl baidu req_header User-Agent Baiduspider #阻止baidu蜘蛛
http_access deny baidu

acl OverConnLimit maxconn 128 #限制同一IP客户端的最大连接数
http_access deny OverConnLimit

acl myip dst 222.18.63.37 #防止被人利用为HTTP代理，设置允许访问的IP地址
http_access deny !myip

acl Manager proto cache_object #允许本地管理
acl Localhost src 127.0.0.1 222.18.63.37
http_access allow Manager Localhost
cachemgr_passwd 53034338 all
http_access deny Manager

acl all src 0.0.0.0/0.0.0.0 #仅仅允许80端口的代理
acl Safe_ports port 80 # http
http_access deny !Safe_ports
http_access allow all

visible_hostname happy.swjtu.edu.cn #Squid信息设置
cache_mgr  ooopic2008@qq.com

cache_effective_user squid #基本设置
cache_effective_group squid
tcp_recv_bufsize 65535 bytes

cache_peer 127.0.0.1 parent 80 0 no-query originserver #2.6的反向代理加速配置

error_directory /usr/local/squid/share/errors/Simplify_Chinese #错误文档

icp_port 0 #单台使用，不使用该功能

hierarchy_stoplist cgi-bin ?

acl QUERY urlpath_regex cgi-bin \? .php .cgi .avi .wmv .rm .ram .mpg .mpeg .zip .exe
cache deny QUERY

acl apache rep_header Server ^Apache
broken_vary_encoding allow apache


refresh_pattern ^ftp:           1440 20%     10080
refresh_pattern ^gopher:        1440 0%    1440
refresh_pattern .             0    20%     4320

cache_store_log none
pid_filename /usr/local/squid/var/logs/squid.pid
emulate_httpd_log on
logformat combined %>a %ui %un [%tl] "%rm %ru HTTP/%rv" %Hs %<st "%{Referer}>h" "%{User-Agent}>h" %Ss:%Sh
cache_log /usr/local/squid/var/logs/cache.log
access_log /usr/local/squid/var/logs/access.log combined
coredump_dir /usr/local/squid/var/cache
cache_dir ufs /usr/local/squid/var/cache 10000 16 256

dns_children 32
hosts_file /etc/hosts

cache_mem 400 MB
cache_swap_low 90
cache_swap_high 95
maximum_object_size 32768 KB
maximum_object_size_in_memory 4096 KB
emulate_httpd_log on

acl picurl url_regex -i \.bmp$ \.png$ \.jpg$ \.gif$ \.jpeg$ #防止盗链
acl mystie1 referer_regex -i happy.swjtu.edu.cn
http_access allow mystie1 picurl
acl nullref referer_regex -i ^$
http_access allow nullref
acl hasref referer_regex -i .+
http_access deny hasref picurl
```
