---
title: 在Docker Compose中配置网络 
date: 2022-08-02 19:00:00
categories: 
  - [虚拟化,Docker Compose]
tags: 
  - Docker Compose
---

> 作者：李旭光
> 引用请标明出处


# Network in Compose 在Docker Compose中配置网络

## 
```
version: "3.9"

services:
  proxy:
    build: ./proxy
    networks:
      - actsnetwork
  app:
    build: ./app
    networks:
      - actsnetwork
  db:
    image: postgres
    networks:
      - testnetwork

 networks:
	actsnetwork:
		name: testnetwork
```