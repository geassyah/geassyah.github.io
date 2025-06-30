---
layout: post
title:  " 云酒馆部署记录 "
date:   2025-06-29 16:30:00 +0800
categories: [Linux, docker]
tags: [Linux, docker, SillyTavern, 云酒馆， Huawei-cloud]
description: " 华为云新加坡有个优惠小鸡，试图利用起来，搭个云酒馆 "
comments: true
published: true
media_subpath: /assets/img/
---

## 前情提要

本来打算用 docker 实现自动化部署，但服务器内存只有 1GB 。
docker 又太占内存，再套个 docker 酒馆就不能加其他东西，遂手动安装。
安装的是 sataging 分支的 1.12.14 版本( 更高版本新增的多密钥功能和本地轮询扩展冲突 )，还有 cloud-save github 云存档插件，

## 部署记录

### 1. 环境配置
```shell
# 使用 nvm 安装 node 和 npm ( debian 版 )
# 1.安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash

# 2.加载环境变量
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

# 3.安装指定版本（ 版本过低酒馆无法运行 ）
nvm install 24.2.0

# 4.设置默认版本
nvm alias default 24.2.0

```

### 2. 主程序源代码下载
```shell
# 克隆 staging 分支并切换到 1.12.14 版本
cd ~
git clone -b staging --depth 1 https://github.com/SillyTavern/SillyTavern.git
cd SillyTavern
git config --global --add safe.directory "*"
git fetch origin tag 1.12.14 --depth 1
git checkout 1.12.14
```

### 3. 插件安装
```shell
# 安装 github 云备份插件及依赖
cd plugins
git clone https://github.com/fuwei99/cloud-saves 
cd cloud-saves
npm install
```

### 4. 主程序安装
```shell
# 安装酒馆依赖并启动
cd ../..
chmod +x ./start.sh
./start.sh 
```

### 5. ( 可选 )扩展安装
```shell
# 为所有用户安装扩展
cd ~/SillyTavern/data/default-user/extensions
git clone https://github.com/N0VI028/JS-Slash-Runner --depth 1
git clone https://github.com/ZerxZ/SillyTavern-Extension-ZerxzLib --depth 1
git clone https://codeberg.org/zonde306/ST-Prompt-Template  --depth 1
```

### 6. ( 可选 ) PM2 设置开机后台自启动
```shell
npm install -g pm2
pm2 start --name sillytavern ./start.sh
pm2 save
pm2 startup

# PM2 日志查看方式
pm2 logs sillytavern
```

### 7. ( 可选 ) docker 卸载
```shell
# 1.停止所有Docker容器和服务
sudo docker stop $(sudo docker ps -aq) 2>/dev/null
sudo systemctl stop docker.socket
sudo systemctl stop docker

# 2.卸载Docker引擎和CLI
sudo apt-get purge -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 3.删除残留文件和配置
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
sudo rm -rf /etc/docker
sudo rm -rf /etc/default/docker
sudo rm -rf /var/run/docker.sock

# 4.删除Docker用户组
sudo groupdel docker

# 5.检查docker命令是否存在
which docker || echo "Docker已成功移除"
```

## 问题与解决
- **云酒馆启动失败，node.js包安装失败，路径下找不到js文件**
  
  报错日志：
  ```shell
  root@hcss-ecs-21ea:/home/SillyTavern# ./start.sh
  Installing Node Modules...

  > sillytavern@1.12.14 postinstall
  > node post-install.js

  Synchronized missing files: ./public/

  up to date in 2s
  Entering SillyTavern...
  node:internal/modules/cjs/loader:1401
    const err = new Error(message);
                ^

  Error: Cannot find module './compile.js'
  Require stack:
  - /home/SillyTavern/node_modules/css-select/lib/index.js
  - /home/SillyTavern/node_modules/cheerio-select/lib/index.js
  - /home/SillyTavern/node_modules/cheerio/dist/commonjs/api/traversing.js
  - /home/SillyTavern/node_modules/cheerio/dist/commonjs/cheerio.js
  - /home/SillyTavern/node_modules/cheerio/dist/commonjs/load.js
  - /home/SillyTavern/node_modules/cheerio/dist/commonjs/load-parse.js
  - /home/SillyTavern/node_modules/cheerio/dist/commonjs/index.js
  - /home/SillyTavern/node_modules/vectra/lib/WebFetcher.js
      at Function._resolveFilename (node:internal/modules/cjs/loader:1401:15)
      at defaultResolveImpl (node:internal/modules/cjs/loader:1057:19)
      at resolveForCJSWithHooks (node:internal/modules/cjs/loader:1062:22)
      at Function._load (node:internal/modules/cjs/loader:1211:37)
      at TracingChannel.traceSync (node:diagnostics_channel:322:14)
      at wrapModuleLoad (node:internal/modules/cjs/loader:235:24)
      at Module.require (node:internal/modules/cjs/loader:1487:12)
      at require (node:internal/modules/helpers:135:16)
      at Object.<anonymous> (/home/SillyTavern/node_modules/css-select/lib/index.js:12:20)
      at Module._compile (node:internal/modules/cjs/loader:1730:14) {
    code: 'MODULE_NOT_FOUND',
    requireStack: [
      '/home/SillyTavern/node_modules/css-select/lib/index.js',
      '/home/SillyTavern/node_modules/cheerio-select/lib/index.js',
      '/home/SillyTavern/node_modules/cheerio/dist/commonjs/api/traversing.js',
      '/home/SillyTavern/node_modules/cheerio/dist/commonjs/cheerio.js',
      '/home/SillyTavern/node_modules/cheerio/dist/commonjs/load.js',
      '/home/SillyTavern/node_modules/cheerio/dist/commonjs/load-parse.js',
      '/home/SillyTavern/node_modules/cheerio/dist/commonjs/index.js',
      '/home/SillyTavern/node_modules/vectra/lib/WebFetcher.js'
    ]
  }

  Node.js v22.17.0

  ```
  安装顺序错误，得将 SillyTavern 完全安装好后，再安装 cloud-save 插件。
  因为两者都涉及到 node.js 依赖包的安装，插件的依赖覆盖了主程序的依赖版本。
  遇到这种情况，单纯清除依赖包和对应缓存是不行的，得直接删除所有文件，重新开始安装。

- **云酒馆经常有红色弹窗，ENOENT ERROR**
  
  ENOENT - "Error No Entity" - 错误实体不存在。
  原因是程序尝试访问的文件或路径不存在，存档部分聊天记录丢失，无视即可。

## 致谢

- Gemini 2.5 pro
- Deepseek V3 0324
- [SillyTavern 官方安装指南](https://docs.sillytavern.app)




