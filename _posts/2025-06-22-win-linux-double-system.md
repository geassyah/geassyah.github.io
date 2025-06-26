---

```markdown
---
layout: post
title:  "Windows + ArchLinux 双系统双硬盘安装"
date:   2025-06-22 21:20:00 +0800
author: "geassyah"
categories: [Linux, Arch]
tags: [Linux, Arch, Windows, 双系统]
description: "已安装 Win10/11，在保留原有 Windows 系统和数据的情况下，使用第二块硬盘安装 Arch Linux，实现双系统独立引导。"
comments: true
published: true
media_subpath: /assets/img/
---

## 前情提要

想在不影响原有 Windows 系统使用的前提下，安装 Arch Linux 系统。查询资料发现，Windows 和 Linux 两个系统安装在两个不同的物理硬盘上，并各自拥有独立的 EFI 引导分区，是最稳定、最不容易出问题的方案。

本文记录了在保留 Windows 硬盘所有数据的基础上，利用第二块硬盘安装 Arch Linux 的完整流程。

## 整体流程概览

---

### **Arch Linux + Windows 双系统双硬盘安装流程**
**(双EFI独立引导，保留原系统数据)**

#### **核心思想**
- **双硬盘双系统：** Windows 和 Arch Linux 安装在不同的物理硬盘上，物理隔离。
- **双EFI独立引导：** 每块硬盘都有自己的 EFI 分区。Windows 更新不会破坏 Arch 的 GRUB 引导，反之亦然。通过 BIOS/UEFI 启动菜单选择启动哪个硬盘，或者通过 GRUB 引导两个系统。
- **保留数据：** 在 Windows 下先为 Arch 准备好安装空间，安装过程中不格式化 Windows 硬盘。

---

### 一、 准备阶段 (在 Windows 下操作)

1.  **备份数据：** **务必备份** 第二块硬盘（计划安装Arch的硬盘）以及 Windows 系统盘上的所有重要数据。
2.  **准备空间：**
    -   进入 Windows 的【磁盘管理】。
    -   在第二块硬盘上，右键点击现有分区，选择【压缩卷】或【删除卷】，腾出至少 60GB（推荐 100GB+）的【未分配空间】。如果第二块硬盘是空的，则跳过此步。
3.  **制作启动盘：** 下载最新的 [Arch Linux ISO](https://archlinux.org/download/)，使用 [Rufus](https://rufus.ie/) 制作 USB 启动盘。
4.  **关闭关键设置：**
    -   重启进入 BIOS/UEFI 设置，关闭 **Secure Boot (安全启动)**。
    -   如果你的 Windows 使用了 BitLocker 加密，请先在 Windows 系统内将其 **关闭**。

---

### 二、 分区与格式化 (在 Arch 安装环境)

1.  **U盘启动**：插入U盘，重启电脑，进入 BIOS/UEFI 设置启动顺序，选择从 U盘 启动，进入 Arch Linux 命令行安装环境。

2.  **连接网络：**
    -   有线：通常会自动连接。使用 `ping baidu.com` 测试。
    -   无线：使用 `iwctl` 命令连接。
      ```bash
      # 进入 iwctl 交互环境
      iwctl
      # 列出无线设备 (如 wlan0)
      device list
      # 扫描网络 (device替换为你的设备名)
      station <device> scan
      # 列出扫描到的网络
      station <device> get-networks
      # 连接网络 (SSID替换为你的WiFi名)
      station <device> connect <SSID>
      # 输入密码后回车，然后输入 quit 退出
      ```

3.  **识别硬盘：** 使用 `lsblk` 或 `fdisk -l` 确认两块硬盘的设备名。例如，Windows 盘可能是 `/dev/nvme0n1`，计划安装 Arch 的第二块硬盘可能是 `/dev/sdb` 或 `/dev/nvme1n1`。**请务必确认正确，避免操作失误。**

4.  **分区 (`cfdisk`)：** 在第二块硬盘的【未分配空间】上创建新分区。
    -   运行 `cfdisk /dev/sdb` (请替换为你的硬盘设备名)。
    -   **EFI 分区：** 大小 `550M` 或更大，类型 (Type) 选择 `EFI System`。
    -   **Swap 分区：** 根据内存大小决定 (如 8G、16G)，类型选择 `Linux swap`。
    -   **根分区 `/`：** 使用剩余的全部空间，类型选择 `Linux filesystem` (默认)。
    -   检查无误后，选择 `[Write]` 写入更改，输入 `yes` 确认，然后 `[Quit]` 退出。

5.  **格式化分区：** 假设 `cfdisk` 后，新分区为 `sdb1` (EFI), `sdb2` (Swap), `sdb3` (Root)。
    ```bash
    # 格式化 EFI 分区为 FAT32
    mkfs.fat -F32 /dev/sdb1

    # 初始化 Swap 分区
    mkswap /dev/sdb2
    # 启用 Swap 分区
    swapon /dev/sdb2

    # 格式化根分区为 Ext4
    mkfs.ext4 /dev/sdb3
    ```

---

### 三、 安装基本系统

1.  **挂载分区：**
    ```bash
    # 挂载根分区到 /mnt
    mount /dev/sdb3 /mnt

    # 创建 boot 目录并挂载 EFI 分区
    # Arch Wiki 推荐 /boot
    mkdir -p /mnt/boot
    mount /dev/sdb1 /mnt/boot
    ```

2.  **安装核心包：**
    ```bash
    # -K 选项会初始化 pacman 密钥环
    # 安装基础包、内核、固件、常用工具和引导程序
    pacstrap -K /mnt base linux linux-firmware vim networkmanager grub efibootmgr os-prober
    ```

---

### 四、 系统配置 (chroot 环境)

1.  **生成 fstab：**
    ```bash
    # -U 选项表示使用 UUID 来标识分区，更稳定
    genfstab -U /mnt >> /mnt/etc/fstab
    ```

2.  **进入新系统环境 (chroot)：**
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
    -   运行 `locale-gen` 生成 locale。
    -   创建 `/etc/locale.conf` 文件，写入 `LANG=en_US.UTF-8` (推荐默认英文，避免 TTY 乱码)。

5.  **网络配置：**
    -   创建 `/etc/hostname`，写入你的主机名 (如 `my-arch`)。
    -   启用网络服务：`systemctl enable NetworkManager`。

6.  **设置密码和用户：**
    -   设置 root 密码：`passwd`。
    -   创建新用户：`useradd -m -G wheel your_username` ( `-G wheel` 将用户添加到 wheel 组，为 sudo 做准备)。
    -   设置用户密码：`passwd your_username`。
    -   安装并配置 sudo：`pacman -S sudo`，然后运行 `visudo` 命令，编辑 `/etc/sudoers` 文件，找到并去掉 `%wheel ALL=(ALL:ALL) ALL` 这一行前面的 `#`。

---

### 五、 配置启动引导 (GRUB)

1.  **安装 GRUB：**
    ```bash
    # --target 指定安装目标为 UEFI
    # --efi-directory 指向 EFI 分区挂载点
    # --bootloader-id 会在 BIOS/UEFI 启动菜单中创建一个名为 "ARCH" 的启动项
    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=ARCH
    ```

2.  **启用 Windows 检测：**
    -   编辑 `/etc/default/grub`。
    -   找到 `#GRUB_DISABLE_OS_PROBER=false` 这一行，去掉前面的 `#`。
    -   (可选) 去掉 `GRUB_CMDLINE_LINUX_DEFAULT` 中的 `quiet`，方便启动时查看日志；添加 `nowatchdog` 避免某些硬件上的内核日志刷屏。

3.  **生成 GRUB 配置 (关键一步)：**
    -   **为了让 Arch 的 GRUB 能找到 Windows，需要临时挂载 Windows 的 EFI 分区。**
    ```bash
    # 假设 Windows 的 EFI 分区是 /dev/nvme0n1p1 (请用 lsblk 确认)
    mkdir -p /mnt/win_efi
    mount /dev/nvme0n1p1 /mnt/win_efi
    ```
    -   **生成最终配置文件**：
    ```bash
    grub-mkconfig -o /boot/grub/grub.cfg
    ```
    -   **检查输出！** 确认看到类似 "Found Windows Boot Manager on /dev/nvme0n1p1@/efi/Microsoft/Boot/bootmgfw.efi" 的字样。

---

### 六、 完成安装

1.  **退出 chroot 并重启：**
    ```bash
    # 卸载临时的 Windows EFI 挂载点
    umount /mnt/win_efi
    # 退出 chroot 环境
    exit
    # 卸载所有分区
    umount -R /mnt
    # 重启
    reboot
    ```

2.  **设置启动项：** 重启后立即进入 BIOS/UEFI，将 **"ARCH"** (或你在 `grub-install` 时设置的 id) 设置为第一启动项。保存并退出。

**安装完成！** 正常情况下，电脑会进入 GRUB 菜单，你可以选择启动 Arch Linux 或 Windows。

### 小技巧

- **SSH 远程安装**：Arch Linux 启动盘默认开启了 SSH 服务。你可以通过 `ip a` 查看 IP 地址，然后用同一局域网内的其他电脑 SSH 连接上去。这样就可以方便地复制粘贴命令，大大提高安装效率。
  ```bash
  # 在 Arch 安装环境中设置一个 root 密码
  passwd
  # 在另一台电脑上连接
  ssh root@<your-arch-ip>
  ```

### 可能问题与解决方案

- **GRUB 引导菜单没有 Windows 选项 / 无法启动到 Windows**
  - **原因**：`os-prober` 未能检测到 Windows。
  - **解决方案**：确认在 `chroot` 环境下，你已经正确地**挂载了 Windows 的 EFI 分区**，并且在 `/etc/default/grub` 中**启用了 `os-prober`**，然后重新运行 `grub-mkconfig -o /boot/grub/grub.cfg`。

- **如何保留 Arch Linux 所在硬盘的原有数据？**
  - **解决方案**：本教程的方案就是为此设计的。只要你在 Windows 的【磁盘管理】中压缩出【未分配空间】来安装 Arch，`cfdisk` 只操作这片未分配空间，就不会动你原有的数据分区。

- **EFI 分区必须在硬盘头部吗？**
  - **答案**：不需要。现代的 UEFI 固件和 GRUB 引导程序不要求 EFI 分区必须在硬盘的起始位置。只要硬盘上存在一个 FAT32 格式并被标记为 `EFI System` 的分区，引导程序就能找到它。因此，无需为了安装而格式化整个硬盘或移动数据。

### 参考资料

- [ArchLinux 安装、配置、维护全指南](https://arch.icekylin.online/guide/rookie/basic-install.html)
- [ArchLinux Vmware安装指北](https://www.cnblogs.com/Thato/p/18311683)
- [Archlinux保姆级教程(安装、源、汉化、输入法、yay)](https://www.bilibili.com/video/BV1jys6eaEtM/)
- [Arch Wiki: Dual boot with Windows](https://wiki.archlinux.org/title/Dual_boot_with_Windows)
