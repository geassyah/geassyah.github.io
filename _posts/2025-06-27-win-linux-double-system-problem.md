---
layout: post
title:  "Windows + ArchLinux 双系统双硬盘使用问题"
date:   2025-06-27 21:20:00 +0800
categories: [Linux, Arch]
tags: [Linux, Arch, Windows, 双系统]
description: "已安装 Win10/11，在保留原有 Windows 系统和数据的情况下，使用第二块硬盘安装 Arch Linux，实现双系统独立引导。"
comments: true
published: true
media_subpath: /assets/img/
---

## 前情提要

- pacman 换源 / 速度慢
  - /etc/config/
- 双系统换行符不兼容
  - 不用 win 编辑器打开
  - sed 批量替换
- git 问题
  - 在 win 的所属硬盘空间创建仓库， VSCode 无法识别仓库
    该路径执行 git ignore 命令
  - 


### 参考资料

- [Archlinux保姆级教程(安装、源、汉化、输入法、yay)](https://www.bilibili.com/video/BV1jys6eaEtM/)
