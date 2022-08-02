---
title: Postgresql绿色版使用
date: 2022-08-02 19:00:00
categories: 
  - [Database,Postgresql]
tags: 
  - Postgresql
---

# 初始化数据库
c:\pgsql\bin\initdb.exe -D c:\pgsql\data -E UTF8

# 注册表
c:\pgsql\bin\pg_ctl register -N postgres -D c:\pgsql\data

# 启动服务
c:\pgsql\bin\postgres -D c:\pgsql\data
