---
layout: post
title:  "Waydroid - APK 在 Linux 上运行的方案"
date:   2025-07-11 10:00:00 +0800
categories: [Linux, 工具]
tags: [waydroid, Linux， APK]
description: "目前不是很成熟，国产软件易闪退，过不了手游模拟器检测"
comments: true
published: true
media_subpath: /assets/img/
---

## 安装部署

### 1. 内核 binder 核心模组和显卡

2025 年 linux、linux-lts、linux-zen 和 linux-lily 内核都已内置 binder，无须配置。

显卡只支持 intel 和 amd 的， Nvida 由于显卡驱动未开源，无法使用。

### 2. 从 AUR 安装

```bash
yay -S waydroid
# 初始化Waydroid，下載含有GAPPS的Android系統映像檔
sudo waydroid init -s GAPPS -f
# 啟動Waydroid容器服務
sudo systemctl start waydroid-container

# 開機自動啟動
sudo systemctl enable waydroid-container
# 點選應用程式列表的「Waydroid」圖示開啟主畫面，或者使用指令：
waydroid show-full-ui
```

### 3. 若 Waydroid 无法联网，开放防火墙，以 UFW 为例

```bash
sudo ufw allow 53
sudo ufw allow 67

sudo ufw default allow FORWARD
sudo ufw reload
sudo ufw status

sudo systemctl restart waydroid-container
```

### 4. 代理问题

使用的是 Clash Verge Rev + 设置 Waydroid 内部代理方案，代理软件需要使用 TUN 模式，并允许局域网连接。

不用配置 https 代理，现代代理软件 http 端口同时支持 HTTP/HTTPS/TCP 流量。

端口按对应代理软件修改，Clash Verge Rev 默认是 7897，其余 Clash 系默认为 7890。

192.168.240.1 IP 为 Waydroid NAT 模式下宿主机的默认 IP 。

```bash
# 设置代理
adb shell settings put global http_proxy "192.168.240.1:7897"
```

```bash
# 取消代理
adb shell settings put global http_proxy :0
```

## 实际体验

测了两个有使用需求的 APP ， Nikke 可正常下载游戏资源，账号登录也没问题，但无法正式进入游戏；番茄免费小说权限全开，启动就闪退。

问题是多种多样的， Nikke 可能是过不了内置的模拟器检测，电脑性能不足；番茄可能是国产软件魔改了一些东西，Waydroid 也还在发展中，兼容性不佳很正常。

不管如何，这不是一个开箱即用、省心的 APK 运行方案，或许转译指令性能极佳，或许以上问题都有解决方案，但和它搏斗了一天的人选择放弃，老老实实选择 Win 平台的模拟器，双系统好处就在这了。

## 参考资料
- [Waydroid - Arch Linux WiKi](https://wiki.archlinuxcn.org/wiki/Waydroid)
- [Waydroid 代理设置 - 知乎](https://zhuanlan.zhihu.com/p/643889264)
- [网络联通问题 ufw 防火墙配置](https://ivonblog.com/posts/archlinux-waydroid/#3-waydroid%E7%9A%84%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7)
- [代理问题 - GitHub Issue](https://github.com/waydroid/waydroid/issues/870)