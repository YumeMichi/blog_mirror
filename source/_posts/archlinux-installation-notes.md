---
title: ArchLinux 安装备忘
date: 2015-11-29 17:46:45
tags:
  - Notes
  - ArchLinux
categories: Linux
---

## 为何安装 ArchLinux

为了更深层次的理解 Linux~ （其实只是闲的蛋疼

## 准备安装介质

U盘首选，没有之一。自己的本子是 MBR 的，UEFI 神马的我才不知道呢哼！

## 制作 U 盘启动

### Linux 上
```
dd if=archlinux-2015.11.01-dual.iso of=/dev/sdb
```
U 盘具体设备自己使用 `lsblk` 命令查看。

### Windows 上
推荐使用 [rufus](https://rufus.akeo.ie/) 这个软件。

<!--more-->

## 开始安装

用制作好的 U 盘启动电脑进入安装环境，32 位还是 64位自行选择。

### 连接到网络

由于 ArchLinux 安装全过程都需要从网络上下载各种包，所以没网的话就去洗洗睡吧～

有线比较简单，用 `ip addr` 查看一下网卡接口型号，比如 `enp2s0`，然后直接启用网卡的 DHCP 功能即可。
```
systemctl enable dhcpcd@enp2s0.service
ping google.com
```
无线网络，使用 `wifi-menu` 查看现有无线列表，然后选择完输入密码即可。其实也是很简单……

### 分区

我是整块硬盘安装一个系统的，有多块硬盘的话具体用 lsblk 查看一下，我因为只有一块硬盘所以可以看到 /dev/sda。
分区工具比较多，推荐 `parted` 或者 `cfdisk，后者有个类似图形化一样的界面很方便。我用的是` parted，表问我为什么，逼格高=。=
```
parted /dev/sda
(parted) mklabel msdos
(parted) mkpart primary ext4 1M 500M
(parted) set 1 boot on
(parted) mkpart primary ext4 500M 50G
(parted) mkpart primary linux-swap 50G 54G
(parted) mkpart primary ext4 54G 100%
```
解释一下 parted 的基本用法
```
(parted) mkpart part-type fs-type start end
```
进入 parted 交互界面后使用 mkpart 创建，后面跟上 4 个参数，分别是 分区类型、文件系统类型、起始点、结束点，分区类型就主分区还是逻辑分区，起始结束点使用 MB、GB 方便计算你懂的。

使用 parted 对 /dev/sda 设备进行分区，分区表 为 MS-DOS 即 MBR 分区结构。共分了4个区，个人习惯～
```
挂载点         大小             说明
------------------------------------------------------------------
/boot         1-500M          用于挂载 /boot 分区，设置为 Bootable。
/             500M-50G        用于挂载 / 分区
swap          50G-54G         用于交换分区(Swap)
/home         54G-100%        剩余空间用于挂载 /home分区
```
分完区后进行格式化
```
mkfs.ext4 /dev/sda1
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda4
mkswap /dev/sda3
```
挂载分区
```
mount /dev/sda2 /mnt
mkdir /mnt/{boot,home}
mount /dev/sda1 /mnt/boot
mount /dev/sda4 /mnt/home
swapon /dev/sda3
```

### 安装基本系统

使用 `pacstrap` 命令
```
pacstrap /mnt base base-devel
```

### 生成 fstab
```
genfstab -p /mnt > /mnt/etc/fstab
```
接下来的操作就要全部切换到这个基本系统上去了。
```
arch-chroot /mnt /bin/bash
```

### 设置硬件时钟
```
hwclock --systohc --utc
```

### 设置系统全局语言

此处 2015 年 12 月 8 日修正
```
echo LANG="en_US.UTF-8" > /etc/locale.conf
```

### 创建 ramdisk
```
mkinitcpio -p linux
```

### 设置 root 用户密码
```
passwd root
```

### 安装 bootloader

一般都是用 grub，因为本人笔记本并不支持 UEFI，所以使用 UEFI 板子的同学得自己查找一下相关的 Wiki 了。
```
pacman -S grub
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

### 设置主机名

给自己去个响亮的名字～
```
echo YumeMichi > /etc/hostname
```
别忘了把自己设置的 hostname 添加到 hosts 文件里哈。
```
nano /etc/hosts

#<ip-address>   <hostname.domain.org>   <hostname>
127.0.0.1       localhost.localdomain   localhost       YumeMichi
::1             localhost.localdomain   localhost       YumeMichi
```

### 网络配置

基本上跟开始安装的时候一样。
有线：
```
systemctl enable dhcpcd@enp2s0.service
```
无线的话注意了，需要安装几个包不然无法使用。
```
pacman -S wpa_supplicant dialog
```

### 添加用户

虽然你也可以直接用 root 用户，但是毕竟不安全，貌似有些软件还不能直接用 root ？
```
useradd -m -g users -G wheel -s /bin/bash ikke
passwd ikke
```

### 安装 sudo

要使用 `sudo` 命令提权的话需要安装 sudo 并且做相应配置
```
pacman -S sudo
```
打开 `/etc/sudoers` 文件，找到 `root ALL=(ALL) ALL` 并依葫芦画瓢添加 `ikke ALL=(ALL) ALL` 即可。

### 卸载分区并重启。

到此系统就安装结束，可以退出安装程序并重启系统了。
```
exit
umount -R /mnt
reboot
```
