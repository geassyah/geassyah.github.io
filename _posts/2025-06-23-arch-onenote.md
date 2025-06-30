---
layout: post
title:  " arch onenote 配置 "
date:   2025-06-23 21:20:00 +0800
categories: [Linux, Arch]
tags: [Linux, onenote]
description: " 在 Arch 安装 onenote 流程记录 "
comments: true
published: true
media_subpath: /assets/img/
---

## 前言

安装的是 P3X-onenote，基本是网页套壳，全局搜索功能不能用，切换页面需要加载一段时间，缺点无法接受，还是得用 wine 版。

## onedrive 配置文件

使用 onedrive --display-config 命令查看当前的配置情况

```yaml
onedrive version                             = vX.Y.Z-A-bcdefghi
Config path                                  = /home/alex/.config/onedrive
Config file found in config path             = true
Config option 'sync_dir'                     = /home/alex/OneDrive
Config option 'enable_logging'               = false
...
Selective sync 'sync_list' configured        = false
Config option 'sync_business_shared_folders' = false
Business Shared Folders configured           = false
Config option 'webhook_enabled'              = false
```

## onedrive 同步文件夹选定

在 $HOME/.config/onedrive 路径下创建 sync_list ，并填入需要的文件或文件夹名，或在 !或- 后面写上不想同步的文件或文件夹名即可，支持通配符，在原仓库的文档中给出了非常详细的描述。

## onedrive 同步命令

```bash
onedrive --synchronize #等同于 onedrive -s
```

## 参考资料

[onedrive(by abraunegg) Linux 下的开源 OneDrive 客户端(cli)](https://zhul.in/2022/12/24/onedrive-abraunegg-recommendation)




