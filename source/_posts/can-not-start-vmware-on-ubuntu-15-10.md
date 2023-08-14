---
hidden: true
title: Ubuntu 15.10 无法启动 VMware 的解决方法
date: 2015-12-06 14:55:37
tags:
  - VMware
  - Ubuntu
categories:
  - Linux
---

昨天闲着无聊给自己的 Ubuntu 安装了一下 GNOME，原本用的是 Unity，这一更新不要紧，结果 VMware 起不来了～

Google 了一圈在 VMware 论坛里发现了一样的问题，虽然不知道为何会导致这样但是解决方案倒是有就是了。

解决方案：https://communities.vmware.com/thread/522779
```bash
echo /usr/lib/vmware/lib/libglibmm-2.4.so.1 | sudo tee /etc/ld.so.conf.d/LD_LIBRARY_PATH.conf
sudo ldconfig
```
<!--more-->

顺便晒一下 GNOME～

![](/img/gnome-my-book.png)

