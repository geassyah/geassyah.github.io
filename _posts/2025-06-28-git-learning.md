---
layout: post
title:  " git 学习笔记 "
date:   2025-06-28 22:20:00 +0800
categories: [Linux]
tags: [Linux, 学习, git]
description: " 记录常用 git 命令以及示例 "
comments: true
published: true
media_subpath: /assets/img/
---

## 常用 git 命令

- **切回固定版本/标签**
```bash
git log #查看提交commit
git checkout tag_name  #签出版本，有时是 switch 
```

## 问题和解决方案
- **仓库克隆失败，扩大缓冲区无效**
  用 ssh 下载仓库，但速度慢。原因是更改记录过多，文件数太多，限定克隆深度即可
```bash
git clone http_repository_url --depth=5  single-branch #限制深度和分支 
```

- **忽视**
  vscode git 仓库无法初始化，无分支
```bash
git config --global --add safe.directory "*" #限制深度和分支 
```
- **获取并切换远程标签，加深仓库深度**
  vscode git 仓库无法初始化，无分支
```bash
git fetch origin tag 1.12.14
git checkout 1.12.14
```

## 参考资料

- [ArchLinux 安装、配置、维护全指南](https://arch.icekylin.online/guide/rookie/pre-install.html)




