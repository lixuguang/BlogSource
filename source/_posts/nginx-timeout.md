---
title: nginx中的超时设置
date: 2022-06-30
categories: 
  - Infra
  - Web服务器
  - Nginx
tags: 
  - Timeout
---

> 作者：李旭光
> 引用请标明出处

# nginx中的超时设置
Nginx主要有四类超时设置：**客户端超时设置**、**DNS解析超时设置**、**代理超时设置**，如果使用ngx_lua，则还有**lua相关的超时**设置。
## 客户端超时设置
对于客户端超时主要设置有**读取请求头超时时间**、**读取请求体超时时间**、**发送响应超时时间**、**长连接超时时间**。通过客户端超时设置避免客户端恶意或者网络状况不佳造成连接长期占用，影响服务端的可处理的能力。

### client_header_timeout  指定等待client发送一个请求头的超时时间
- 语法: client_header_timeout time
- 默认值: 60s
- 上下文: http server
- 说明: 指定等待client发送一个请求头的超时时间（例如：GET / HTTP/1.1）.仅当在一次read中，没有收到请求头，才会算成超时。如果在超时时间内，client没发送任何东西，nginx返回HTTP状态码408(“Request timed out”)

### client_body_timeout   该指令设置请求体（request body）的读超时时间
- 语法: client_body_timeout time
- 默认值: 60s
- 上下文: http server location
- 说明: 该指令设置请求体（request body）的读超时时间。仅当在一次readstep中，没有得到请求体，就会设为超时。超时后，nginx返回HTTP状态码408(“Request timed out”)

### send_timeout time
设置发送响应到客户端的超时时间，默认为60s，此超时时间指的也是两次成功写操作间隔时间，而不是发送整个响应的超时时间。如果在此超时时间内客户端没有接收任何响应，则Nginx关闭此连接。

### keepalive_timeout 第一个参数指定了与client的keep-alive连接超时时间
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
  
## DNS解析超时设置
  
### resolver_timeout 设置DNS解析超时时间
- 语法 resolver_timeout time 
- 默认值 30s
- 上下文 http server location
- 说明 该指令设置DNS解析超时时间

## 代理超时设置
### proxy_connect_timeout 设置与upstream server的连接超时时间，不能超过75秒
- 语法 proxy_connect_timeout time 
- 默认值 60s
- 上下文 http server location
- 说明 该指令设置与upstream server的连接超时时间，有必要记住，这个超时不能超过75秒。
这个不是等待后端返回页面的时间，那是由proxy_read_timeout声明的。如果你的upstream服务器起来了，但是hanging住了（例如，没有足够的线程处理请求，所以把你的请求放到请求池里稍后处理），那么这个声明是没有用的，由于与upstream服务器的连接已经建立了。

### proxy_read_timeout 设置与代理服务器的读超时时间
- 语法 proxy_read_timeout time 
- 默认值 60s
- 上下文 http server location
- 说明 该指令设置与代理服务器的读超时时间。它决定了nginx会等待多长时间来获得请求的响应。这个时间不是获得整个response的时间，而是两次reading操作的时间。

### proxy_send_timeout 设置发送请求给upstream服务器的超时时间
- 语法 proxy_send_timeout time 
- 默认值 60s
- 上下文 http server location
- 说明 这个指定设置了发送请求给upstream服务器的超时时间。超时设置不是为了整个发送期间，而是在两次write操作期间。如果超时后，upstream没有收到新的数据，nginx会关闭连接

#### 失败重试机制设置。

proxy_next_upstream error | timeout | invalid_header | http_500 | http_502 | http_503 | http_504 |http_403 | http_404 | non_idempotent | off ...：配置什么情况下需要请求下一台上游服务器进行重试。默认为“errortimeout”。error表示与上游服务器建立连接、写请求或者读响应头出错。timeout表示与上游服务器建立连接、写请求或者读响应头超时。invalid_header表示上游服务器返回空的或错误的响应头。http_XXX表示上游服务器返回特定的状态码。non_idempotent表示RFC-2616定义的非幂等HTTP方法（POST、LOCK、PATCH），也可以在失败后重试下一台上游服务器（即默认幂等方法GET、HEAD、PUT、DELETE、OPTIONS、TRACE才可以重试）。off表示禁用重试。

重试不能无限制进行，因此，需要如下两个指令控制重试次数和重试超时时间。

proxy_next_upstream_tries number：设置重试次数，默认0表示不限制，注意此重试次数指的是所有请求次数（包括第一次和之后的重试次数之和）。

proxy_next_upstream_timeout time：设置重试最大超时时间，默认0表示不限制。

即在proxy_next_upstream_timeout时间内允许proxy_next_upstream_tries次重试。如果超过了其中一个设置，则Nginx也会结束重试并返回客户端响应（可能是错误码）。

如下配置表示当error/timeout时重试upstream中的下一台上游服务器，如果重试的总时间超出了6s或者重试了1次，则表示重试失败（因为之前已经请求一次了，所以还能重试一次），Nginx结束重试并返回客户端响应。

## upstream存活超时设置
max_fails和fail_timeout：配置什么时候Nginx将上游服务器认定为不可用/不存活。当上游服务器在fail_timeout时间内失败了max_fails次，则认为该上游服务器不可用/不存活。并在接下来的fail_timeout时间内从upstream摘掉该节点（即请求不会转发到该上游服务器）。

什么情况下被认定为失败呢？其由 proxy_next_upstream定义，不过，不管 proxy_next_upstream如何配置，error, timeout and invalid_header 都将被认为是失败。

如server 192.168.61.1:9090max_fails=2 fail_timeout=10s;表示在10s内如果失败了2次，则在接下来的10s内认定该节点不可用/不存活。这种存活检测机制是只有当访问该上游服务器时，采取惰性检查，可以使用ngx_http_upstream_check_module配置主动检查。

max_fails设置为0表示不检查服务器是否可用（即认为一直可用），如果upstream中仅剩一台上游服务器时，则该服务器是不会被摘除的，将从不被认为不可用。
### proxy_upstream_fail_timeout（fail_timeout）设置某一个upstream后端失败了指定次数（max_fails）后，该后端不可操作的时间
- 语法 server address [fail_timeout=30s]
- 默认值 10s
- 上下文 upstream
- 说明 Upstream模块下 server指令的参数，设置了某一个upstream后端失败了指定次数（max_fails）后，该后端不可操作的时间，默认为10秒
  
## lingering_timeout
- 语法: lingering_timeout time
- 默认值: 5s
- 上下文: http server location
- 说明: lingering_close生效后，在关闭连接前，会检测是否有用户发送的数据到达服务器，如果超过lingering_timeout时间后还没有数据可读，就直接关闭连接；否则，必须在读取完连接缓冲区上的数据并丢弃掉后才会关闭连接。





