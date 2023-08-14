---
hidden: true
title: 一个 GNOME Terminal 无法运行引发的问题
date: 2015-12-08 05:41:37
tags:
  - Linux
  - ArchLinux
  - GNOME
categories:
  - Linux
---

## 问题思考
最近想给 ArchLinux 装个 GNOME，就按照[官方 Wiki](https://wiki.archlinux.org/index.php/GNOME) 的步骤安装了 `gnome` 和 `gnome-extra` 两个包，也顺利启动到了 GNOME 桌面下，但是碰到了一个头疼的问题，就是终端启动不了......

经过多方搜索以及求助于大神，最终确定是 `locale.conf` 的问题，ArchLinux 于 Ubuntu 之类的系统不同，并不会自动生成 `locale.conf` 文件，而这个文件是 GNOME Terminal 运行所必须的（天哪噜！这个要不是问了大神我都不知道，GNOME 的 Wiki 页面也没写道...）。

那么这个文件是需要用户在安装系统的时候手动创建的，这也是我在前面一篇 [ArchLinux 安装笔记](https://blog.ikke.moe/posts/archlinux-installation-notes/)中忽略的问题，应该说是我自己看文档不够认真的问题，已经在文章中更正了。

这里就引出了一个问题，到底那些东西是需要在安装的时候必须设置的？事实上像 `hostname`、`locale`、`timezone` 等这些东西甚至是 `passwd` 都不是必须的，也就是不设置这些东西系统照样能够安装完、能够启动，而 Ubuntu 这类的发行版则会帮你做完这些事（事实上这些也是在安装的时候让用户设置的），只不过 ArchLinux 不会提醒你这些就是了～
<!--more-->

## 解决方法
解决方法就是在 `/etc` 目录下创建一个 `locale.conf`，内容为 `LANG="en_US.UTF-8"`。
对中文用户者而言，不建议改成 `LANG="zh_CN.UTF-8"`，因为可能会导致部分软件乱码，因为 `/etc/locale.conf` 是全局的语言配置文件，如果是用户使用的在 `~/.bashrc` 中配置。
