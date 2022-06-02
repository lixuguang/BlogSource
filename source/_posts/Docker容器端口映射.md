# Docker容器端口映射
常见容器服务需要做端口映射，这里以nginx为例进行举例

## 启动一个nginx容器
docker run -itd --name nginx1 -P nginx:latest #随机端口
docker run -itd --name nginx2 -p 6001:80 nginx:latest #指定端口
docker run -itd --name nginx3 -p 172.16.10.1:6002:80 nginx:latest #指定IP和端口
