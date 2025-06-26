---
layout: post
title:  " Windows + ArchLinux 双系统双硬盘安装 "
date:   2025-06-22 21:20:00 +0800
author: "geassyah"
categories: [Linux, Arch]
tags: [Linux, Arch, Windows, 双系统]
description: "已安装 Win10 ，在保留原有数据的情况下，再安装 Arch Linux"
comments: true
published: true
media_subpath: /assets/img/
---

## 前情提要

想在不影响原有 Win 系统使用的前提下，安装 Arch Linux 系统。查询资料发现，Win 和 Linux 两个系统安装在两个不同的的物理硬盘是个稳妥的方案。

以下是根据最后的两篇参考资料，写的简要教程，具体实行步骤参考原文。

## 整体大概流程


---

### **Arch Linux + Win10 双系统双硬盘安装流程**
**(双EFI独立引导，保留数据盘)**

#### **核心思想**
- **双硬盘双系统：** Windows和Arch Linux安装在不同物理硬盘上。
- **双EFI独立：** 每块硬盘都有自己的EFI分区，互不干扰。Windows更新不会破坏Arch的启动。
- **保留数据：** 在Windows下先压缩出空间，Arch安装时不格式化原数据分区。

---

#### **一、 准备阶段 (在Windows下操作)**

1.  **备份数据：** 备份第二块硬盘（计划安装Arch的硬盘）上的所有重要数据。
2.  **准备空间：**
    -   进入Win10的【磁盘管理】。
    -   右键点击第二块硬盘的数据分区，选择【压缩卷】。
    -   压缩出至少60GB（推荐100GB+）的【未分配空间】。
3.  **制作启动盘：** 下载Arch Linux ISO，使用 Rufus 制作USB启动盘。
4.  **关闭安全启动：** 重启进入BIOS/UEFI设置，关闭 **Secure Boot**。

---

#### **二、 分区与格式化 (在Arch安装环境)**

1.  **U盘启动**，进入Arch命令行安装环境。
2.  **连接网络：**
    -   有线：通常自动连接。
    -   无线：使用 `iwctl` 命令连接。
3.  **识别硬盘：** 使用 `lsblk` 或 `fdisk -l` 确认第二块硬盘的设备名 (如 `/dev/sdb` 或 `/dev/nvme1n1`)。
4.  **分区 (`cfdisk`)：** 在【未分配空间】上创建新分区。
    -   `cfdisk /dev/sdb` (请替换为你的设备名)
    -   **EFI分区：** 大小 `550M` 或更大，类型 `EFI System`。
    -   **Swap分区：** 根据内存大小决定 (如 `8G`)，类型 `Linux swap`。
    -   **根分区 `/`：** 使用剩余全部空间，类型 `Linux filesystem`。
    -   选择 `[Write]` 写入更改，然后 `[Quit]` 退出。
5.  **格式化分区：** (假设新分区为 `sdb1`, `sdb2`, `sdb3`)


#### **三、 安装基本系统**

1.  **挂载分区：**

2.  **安装核心包：**


---

#### **四、 系统配置 (chroot环境)**

1.  **生成fstab：**
    ```bash
    genfstab -U /mnt >> /mnt/etc/fstab
    ```
2.  **进入新系统环境 (chroot):**
    ```bash
    arch-chroot /mnt
    ```
3.  **设置时区和时间：**
    ```bash
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
    hwclock --systohc
    ```
4.  **本地化 (语言)：**
    -   编辑 `/etc/locale.gen`，去掉 `en_US.UTF-8 UTF-8` 和 `zh_CN.UTF-8 UTF-8` 前的 `#`。
    -   运行 `locale-gen`。
    -   创建 `/etc/locale.conf` 文件，写入 `LANG=en_US.UTF-8`。
5.  **网络配置：**
    -   创建 `/etc/hostname`，写入你的主机名 (如 `my-arch`)。
    -   启用网络服务：`systemctl enable NetworkManager`。
6.  **设置密码和用户：**
    -   设置root密码：`passwd`。
    -   创建新用户：`useradd -m your_username`。
    -   设置用户密码：`passwd your_username`。
    -   (可选但推荐) 给予sudo权限：`pacman -S sudo`，然后编辑 `/etc/sudoers` (使用 `visudo` 命令)，去掉 `%wheel ALL=(ALL:ALL) ALL` 前的 `#`，并将你的用户添加到 `wheel` 组：`usermod -aG wheel your_username`。

---

#### **五、 配置启动引导 (GRUB)**

1.  **安装GRUB：**
    ```bash
    # --bootloader-id=ARCH 会在BIOS中创建一个名为"ARCH"的启动项
    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=ARCH
    ```
2.  **启用Windows检测：**
    -   编辑 `/etc/default/grub`。
    -   去掉 `#GRUB_DISABLE_OS_PROBER=false` 这一行的 `#`。
    -   (可选) 去掉 `GRUB_CMDLINE_LINUX_DEFAULT` 中的 `quiet`，添加 `nowatchdog`。
3.  **生成GRUB配置 (关键一步)：**
    -   **挂载Windows的EFI分区** 以便 `os-prober` 能找到它。
        ```bash
        # 假设Win的EFI分区是 /dev/nvme0n1p1
        mkdir -p /mnt/win_efi
        mount /dev/nvme0n1p1 /mnt/win_efi
        ```
    -   **生成最终配置文件**：
        ```bash
        grub-mkconfig -o /boot/grub/grub.cfg
        ```
    -   **确认输出中包含 "Found Windows Boot Manager" 字样。**

---

#### **六、 完成安装**

1.  **退出 chroot 并重启：**
    ```bash
    umount /mnt/win_efi   # 卸载临时的Win EFI挂载
    exit                  # 退出 chroot
    umount -R /mnt        # 卸载所有分区
    reboot                # 重启
    ```
2.  **设置启动项：** 重启后进入BIOS/UEFI，将 **"ARCH"** 设置为第一启动项。

**安装完成！** 现在开机会进入GRUB菜单，可以选择启动Arch Linux或Windows。

## 准备

1. ArchLinux 启动盘( > 2G )
2. Windows 磁盘管理释放空间，最好 > 70 GB ≈ /EFI ( ≥ 512 MB) + /swap ( ≥ RAM * 0.6) + / 根分区 ( ≥ 60 GB )
3. 关闭 Bios 的 Secure Boot 安全启动选项
4. 关闭 Bitlocker 硬盘加密，Win11 默认开启 Bitlocker
5. 确保网络连接，记住 WiFi 名(必须英文)和密码，或者有线连接x
6. 上移 Bios U盘启动顺序，设置为U盘启动

## 小技巧

- SSH 远程连接，快速复制粘贴命令
- 

## 难点

创建分区

## 可能问题与解决方案

- grup 引导启动没有 Windows 选项
  Windows 系统无法被引导识别到，未挂载 Windows 系统盘
- EIP 分区并不要求一定在硬盘首部
  现在的 GRUB 系统引导并不要求 EIP 分区一定在硬盘首部，只要硬盘上存在 EIP 分区，就可以自动识别，不需要格式化硬盘，也不需要把原有数据后移。


## 参考资料

- [ArchLinux 安装、配置、维护全指南](https://arch.icekylin.online/guide/rookie/pre-install.html)
- [ArchLinux Vmware安装指北](https://www.cnblogs.com/Thato/p/18311683)



