---
hidden: true
title: SudaMod 1.1 编译教程
date: 2016-01-22 14:10:47
tags:
  - Android
  - SudaMod
  - CyanogenMod
categories:
  - 教程
---

## 关于 SudaMod

SudaMod 是基于 CyanogenMod 二次开发的系统，通过合并优秀第三方代码，加上自己的特色功能，让系统变得更加好用。

SudaMod 致力于打造一个纯净的本土化系统，在保留原生系统功能的同时加以提升，给机油们带来不一样的体验。

更多介绍可以点击查看开发者的[说明](http://www.oneplusbbs.com/thread-694014-1-1.html)

## 关于 OnePlus X

OnePlus X(一加X，手机代号 onyx) 是一加科技于 2015 年 10 月 29 日发布的一款新手机，按照配置可以分为基础版、标准版和陶瓷版，跟本文就不在赘述，详细可看 Wikipedia 的词条 [OnePlus X](https://en.wikipedia.org/wiki/OnePlus_X)。

<!--more-->
手机配置一览，只能说非常一般......

| Feature                 | Specification                     |
| :---------------------- | :-------------------------------- |
| CPU                     | Quard-core 2.3 GHz                |
| Chipset                 | Qualcomm Snapdragon 801 MSM8974AA |
| GPU                     | Adreno-330                        |
| Memory                  | (2/3)(Basic/Standard) GB RAM      |
| Shipped Android Version | 5.1.1                             |
| Storage                 | 16 GB                             |
| MicroSD                 | Up to 128 GB                      |
| Battery                 | 2525 mAh                          |
| Dimensions              | 140 x 69 x 6.9 mm                 |
| Display                 | 1080 x 1920 pixels                |
| Camera                  | 13 MP(Back), 8 MP(Front)          |
| Release Date            | October 2015                      |

## 搭建编译环境

### 系统要求

建议使用 Ubuntu 15.04 及以上的版本进行编译，内存 4GB 以上，硬盘空间 100GB 左右。可以使用虚拟机但是不建议，另外机器配置最好是越高越好～

### 更新系统

```bash
apt-get update && apt-get upgrade
```

### 安装编译所需的基本软件包

```bash
apt-get install bison build-essential curl flex git gnupg gperf libesd0-dev libncurses5-dev libsdl1.2-dev libwxgtk2.8-dev libxml2 libxml2-utils lzop openjdk-7-jdk openjdk-7-jre pngcrush schedtool squashfs-tools xsltproc zip zlib1g-dev g++-multilib gcc-multilib lib32ncurses5-dev unzip lib32readline6-dev lib32z1-dev git python2.7
```

### 设置 Git 信息

如果不做提交代码用可以随意填写
```bash
git config --global user.name "xxx_username"
git config --global user.email "xxx_mail@gmail.com"
```

### 设定 repo

```bash
mkdir -p ~/android/{bin,sm}
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/android/bin/repo
chmod +x ~/android/bin/repo
echo "export PATH=~/android/bin:$PATH" >> ~/.bashrc
source ~/.bashrc
```

### 同步 SudaMod 源码

```bash
cd ~/android/sm
repo init -u git://github.com/SudaMod/android.git -b sm-1.1
repo sync --force-sync -j8
```

### 同步设备代码和内核

```bash
mkdir ~/android/sm/.repo/local_manifests
wget -P ~/android/sm/.repo/local_manifests https://raw.githubusercontent.com/YumeMichi/android_local_manifests/sm-1.1/local_manifests.xml
repo sync --force-sync -j8
```

### 去除多余的文件

因为设备代码里已经有 keyhandler、configpanel 和 cmhw 了。
```bash
rm -rf ~/android/sm/device/oppo/common/{keyhandler,configpanel,cmhw}
```

## 编译 ROM

### 初始化编译环境
```
cd /android/sm
. build/envsetup.sh
lunch sm_onyx-userdebug
```

### 编译 ROM

```
make bacon -j8
```

等待编译完成后，ROM 会生成在 `/android/sm/out/target/product/onyx` 下，拷贝到手机里进入 recovery 双清后刷入即可。
