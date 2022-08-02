---
title: 在Docker Compose中配置网络 
date: 2022-08-02 19:00:00
categories: 
  - [Docker]
tags: 
  - 镜像安全
---

# Docker 镜像安全最佳实践 

## Docker 和 宿主机 的设置
1. 保证宿主机和 Docker 的版本是最新的
2. 不要暴露 Docker 的 守护进程（daemon） 的套接字
3. 使用 rootless 模式启动 Docker
4. 避免使用特权容器
5. 限制容器资源
6. 隔离容器网络
7. 提高容器的隔离度
8. 将文件系统和卷设置为只读
9. 完整的生命周期管理
10. 限制来自容器内的系统调用

## 确保镜像安全
11. 扫描和验证容器镜像
12. 使用最小基础镜像
13. 不要向 Docker 镜像泄露敏感信息
14. 使用多阶段构建
15. 确保容器注册
16. 使用固定标签以获得不变性

## 监控容器
17. 监控容器活动
18. 确保容器在运行时的安全
19. 将故障排除数据与容器分开保存
20. 为镜像使用元数据标签


## 参考
![Top 20 Dockerfile best practices](https://sysdig.com/blog/dockerfile-best-practices/)
![Dockerセキュリティベストプラクティス トップ20](https://www.creationline.com/lab/aquasecurity/43087)