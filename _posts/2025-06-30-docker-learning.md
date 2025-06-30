---
layout: post
title:  " docker 学习笔记 "
date:   2025-06-30 09:00:00 +0800
categories: [Linux, docker]
tags: [Linux, docker, learing]
description: " 常用 docker 命令及相关问题 "
comments: true
published: true
media_subpath: /assets/img/
---


## 常用 docker 命令

### 系统命令
```bash
sudo systemctl stop docker  #停用 docker 
sudo systemctl start docker  #启用 docker 
sudo systemctl restart docker  #重启 docker 
```

### 容器查看
```bash
docker container ls -a #能用，但太长了，放弃

docker ps #只有正在运行的容器
docker ps -a  #列出所有容器（包括已停止的）
docker ps -l #显示最后创建容器 - last
docker ps -f "name=my_container" #过滤筛选容器信息，不能用grep

docker logs <container_name/id> -f #实时监控容器日志 - follow
docker top <container_name/id> #容器进程top( Table of Processes ），ps( process status )，top持续监控，ps瞬时
docker inspect container_id #查看容器详细信息
```

### 容器操作
```bash
docker container rm -f CONTAINER #强制删除容器（等同下面两条） - force 
docker container stop CONTAINER  #停用容器
docker container rm CONTAINER  #删除容器 - remove
```

### 镜像操作
```bash
docker images #列出所有镜像
```


## 参考资料

- [docker top 命令 - 菜鸟教程](https://www.runoob.com/docker/docker-top-command.html)
- [Linux top 命令 - 菜鸟教程](https://www.runoob.com/linux/linux-comm-top.html)




