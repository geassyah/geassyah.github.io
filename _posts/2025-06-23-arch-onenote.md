---
layout: post
title:  " arch onenote 配置 "
date:   2025-06-23 21:20:00 +0800
author: "geassyah"
categories: [Linux, Arch]
tags: [Linux, onenote]
description: " 简单记录 docker 命令以及遇到的问题 "
comments: true
published: true
media_subpath: /assets/img/
---

## onedrive 配置文件

使用 onedrive --display-config 命令查看当前的配置情况

```routeros
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
onedrive --synchronize
```

## 参考资料

[onedrive(by abraunegg) Linux 下的开源 OneDrive 客户端(cli)](https://zhul.in/2022/12/24/onedrive-abraunegg-recommendation)




