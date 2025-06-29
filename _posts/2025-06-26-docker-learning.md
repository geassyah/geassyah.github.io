---
layout: post
title:  " docker 学习笔记 "
date:   2025-06-26 22:20:00 +0800
categories: [Linux, Arch]
tags: [Linux, docker]
description: " 简单记录 docker 命令以及遇到的问题 "
comments: true
published: true
media_subpath: /assets/img/
---


## 常用 docker 命令

- **系统命令**
```bash
sudo systemctl stop docker  #停用 docker 
sudo systemctl start docker  #启用 docker 
sudo systemctl restart docker  #重启 docker 
```

- **系统命令**
```bash
docker ps -a  #列出所有容器
docker stop container_id/container_name  #停用容器
docker remove container_id/container_name  #删除容器 
```

- **系统命令**
```bash
sudo systemctl stop docker  #停用 docker 
sudo systemctl start docker  #启用 docker 
sudo systemctl restart docker  #重启 docker 
```




## 参考资料

- [ArchLinux 安装、配置、维护全指南](https://arch.icekylin.online/guide/rookie/pre-install.html)




