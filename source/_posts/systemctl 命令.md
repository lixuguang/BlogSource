---
title: systemctl 命令
date: 2022-08-02 18:00:00
categories: 
  - [linux,systemctl]
tags: 
  - systemctl
---

# systemctl 命令

## systemctl
范列出系统上面有启动的unit 

## systemctl list-unit-files
列出所有已经安装的unit有哪些

## systemctl list-units --type=service --all
列出类型为service的所有项目，不论启动与否

## systemctl get-default
输入目前机器默认的模式，如图形界面模式或者文本模式

## systemctl isolate multi-user.target
将目前的操作环境改为纯文本模式，关掉图形界面

## systemctl isolate graphical.target
将目前的操作环境改为图形界面

## systemctl poweroff
系统关机

## systemctl reboot
重新开机

## systemctl suspend
进入暂停模式

## systemctl rescue
强制进入救援模式

## systemctl hibernate
进入休眠模式

## systemctl emergency
强制进入紧急救援模式

## systemctl list-dependencies --reverse
查询当前默认的target关联了啥

## systemctl list-dependencies graphical.target
查询图形界面模式的target关联了啥

## systemctl list-sockets
查看当前的socket服务

## systemctl show etcd.service
查看 unit 的详细配置情况

## systemctl mask etcd.service
禁用某个服务

## systemctl unmask etcd.service
解除禁用某个服务