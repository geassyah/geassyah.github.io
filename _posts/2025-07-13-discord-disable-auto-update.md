---
layout: post
title:  "discord 禁止自动更新"
date:   2025-07-13 16:00:00 +0800
categories: [Linux, 工具, 杂项]
tags: [discord, Linux]
description: "linux 和 windows 版都有"
comments: true
published: true
media_subpath: /assets/img/
---

## discord 禁止自动更新

### Linux 版

编辑文件，在最后一个花括号之后添加以下行：
"SKIP_HOST_UPDATE": true

```bash
nano ~/.config/discord/settings.json
```

最终效果类似：

```yaml
{
 "BACKGROUND_COLOR": "#202225",
 "IS_MAXIMIZED": false,
 "IS_MINIMIZED": false,
 "WINDOW_BOUNDS": {
   "x": 0,
   "y": 0,
   "width": 1920,
   "height": 1080
  },
 "SKIP_HOST_UPDATE": true
}
```

### Windows 版

文件在：~/Users/AppData/Roaming/discord/settings.json，其他相同。


## 参考资料
- [discord_disable_auto_update - Reddit](https://www.reddit.com/r/openSUSE/comments/i8v70t/comment/idtjcj2)