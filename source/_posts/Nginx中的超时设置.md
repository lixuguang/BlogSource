---
title: Nginx中的超时设置
date: 2022-08-02 19:00:00
categories: 
  - [Web服务器,Nginx]
tags: 
  - 超时设置
---

> 作者：李旭光
> 引用请标明出处


# Nginx中的超时设置

## client_header_timeout  指定等待client发送一个请求头的超时时间
- 语法: client_header_timeout time
- 默认值: 60s
- 上下文: http server
- 说明: 指定等待client发送一个请求头的超时时间（例如：GET / HTTP/1.1）.仅当在一次read中，没有收到请求头，才会算成超时。如果在超时时间内，client没发送任何东西，nginx返回HTTP状态码408(“Request timed out”)

## client_body_timeout   该指令设置请求体（request body）的读超时时间
- 语法: client_body_timeout time
- 默认值: 60s
- 上下文: http server location
- 说明: 该指令设置请求体（request body）的读超时时间。仅当在一次readstep中，没有得到请求体，就会设为超时。超时后，nginx返回HTTP状态码408(“Request timed out”)

## keepalive_timeout 第一个参数指定了与client的keep-alive连接超时时间
- 语法: keepalive_timeout timeout [ header_timeout ]
- 默认值: 75s
- 上下文: http server location
- 说明: 第一个参数指定了与client的keep-alive连接超时时间。服务器将会在这个时间后关闭连接。可选的第二个参数指定了在响应头Keep-Alive: timeout=time中的time值。这个头能够让一些浏览器主动关闭连接，这样服务器就不必要去关闭连接了。没有这个参数，nginx不会发送Keep-Alive响应头（尽管并不是由这个头来决定连接是否“keep-alive”）
两个参数的值可并不相同

  - 注意不同浏览器怎么处理“keep-alive”头
  - MSIE和Opera忽略掉"Keep-Alive: timeout=<N>" header.
  - MSIE保持连接大约60-65秒，然后发送TCP RST
  - Opera永久保持长连接
  - Mozilla keeps the connection alive for N plus about 1-10 seconds.
  - Konqueror保持长连接N秒

## lingering_timeout
- 语法: lingering_timeout time
- 默认值: 5s
- 上下文: http server location
- 说明: lingering_close生效后，在关闭连接前，会检测是否有用户发送的数据到达服务器，如果超过lingering_timeout时间后还没有数据可读，就直接关闭连接；否则，必须在读取完连接缓冲区上的数据并丢弃掉后才会关闭连接。

## resolver_timeout 设置DNS解析超时时间
- 语法 resolver_timeout time 
- 默认值 30s
- 上下文 http server location
- 说明 该指令设置DNS解析超时时间

## proxy_connect_timeout 设置与upstream server的连接超时时间，不能超过75秒
- 语法 proxy_connect_timeout time 
- 默认值 60s
- 上下文 http server location
- 说明 该指令设置与upstream server的连接超时时间，有必要记住，这个超时不能超过75秒。
这个不是等待后端返回页面的时间，那是由proxy_read_timeout声明的。如果你的upstream服务器起来了，但是hanging住了（例如，没有足够的线程处理请求，所以把你的请求放到请求池里稍后处理），那么这个声明是没有用的，由于与upstream服务器的连接已经建立了。

## proxy_read_timeout 设置与代理服务器的读超时时间
- 语法 proxy_read_timeout time 
- 默认值 60s
- 上下文 http server location
- 说明 该指令设置与代理服务器的读超时时间。它决定了nginx会等待多长时间来获得请求的响应。这个时间不是获得整个response的时间，而是两次reading操作的时间。

## proxy_send_timeout 设置发送请求给upstream服务器的超时时间
- 语法 proxy_send_timeout time 
- 默认值 60s
- 上下文 http server location
- 说明 这个指定设置了发送请求给upstream服务器的超时时间。超时设置不是为了整个发送期间，而是在两次write操作期间。如果超时后，upstream没有收到新的数据，nginx会关闭连接

## proxy_upstream_fail_timeout（fail_timeout）设置某一个upstream后端失败了指定次数（max_fails）后，该后端不可操作的时间
- 语法 server address [fail_timeout=30s]
- 默认值 10s
- 上下文 upstream
- 说明 Upstream模块下 server指令的参数，设置了某一个upstream后端失败了指定次数（max_fails）后，该后端不可操作的时间，默认为10秒