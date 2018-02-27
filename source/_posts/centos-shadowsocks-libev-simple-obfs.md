---
title: CentOS 搭建 Shadowsocks-libev 环境
date: 2017-05-23 02:47:29
tags:
  - CentOS
  - Shadowsocks
categories:
  - Note
---

## 前言
之前 Vultr 刚出 2.5 美元套餐的时候开了一台东京机房的 VPS 一直没使用，因为我知道肯定要卖光，最近也正好有时间，把原来 5 美元的机子数据备份了一下，准备切换到新的机子上。

之前用的是 CentOS 6 + 锐速的组合，虽然软件版本和内核都比较旧，但是锐速的 TCP 加速确实非常好用，这次则是想试一下 BBR，这个也是一个 TCP 拥塞控制算法，从 Linux 4.9 开始支持该算法，但是需要手动启用。

关于 BBR 可以看 [使用TCP时序图解释BBR拥塞控制算法的几个细节](http://blog.csdn.net/dog250/article/details/72042516)，以及 [Ubuntu 上搭建 SS + BBR 的笔记](https://github.com/YumeMichi/homeserver-config/blob/master/vultr-ubuntu-bbr-shadowsocks-libev-simple-obfs.md)，使用了几天发现效果并不尽如人意，至少从平时的命令行操作上甚至比不启用 BBR 还要卡，而且使用上有个非常致命的问题 —— 速度极度的不稳定。

因为 VPS 对我来说主要还是用于梯子，领教过从 GitHub 上拉代码几 KB 速度的人应该能够感同身受，所以这种时快时慢的梯子根本没法用，最后还是用回 CentOS + 锐速的黄金组合。

只是这次，我选择了 CentOS 7，怎么说呢起码软件包要新一些而且也不会影响到锐速的使用。

<!--more-->


## 安装依赖
安装一些基本的编译工Diary1具和依赖。
```
yum install gcc autoconf libtool automake make zlib-devel openssl-devel asciidoc xmlto libsodium-devel libudns-devel udns-devel libev-devel
```

## 安装 Shadowsocks-libev
EPEL 源有最新的二进制安装包，所以就不需要自己编译了。
```
cd /etc/yum.repos.d
wget https://copr.fedorainfracloud.org/coprs/librehat/shadowsocks/repo/epel-7/librehat-shadowsocks-epel-7.repo
yum makecache
yum install shadowsocks-libev
```

修改一下服务文件 `/etc/systemd/system/multi-user.target.wants/shadowsocks-libev.service`，配置一下运行的用户和用户组以及配置文件加载路径，例如：
```
#  This file is part of shadowsocks-libev.
#
#  Shadowsocks-libev is free software; you can redistribute it and/or modify
#  it under the terms of the GNU General Public License as published by
#  the Free Software Foundation; either version 3 of the License, or
#  (at your option) any later version.
#
#  This file is default for Debian packaging. See also
#  /etc/default/shadowsocks-libev for environment variables.

[Unit]
Description=Shadowsocks-libev Default Server Service
Documentation=man:shadowsocks-libev(8)
After=network.target

[Service]
Type=simple
EnvironmentFile=/etc/default/shadowsocks-libev
User=root
Group=root
LimitNOFILE=32768
ExecStart=/usr/bin/ss-server -c /etc/shadowsocks-libev/config.json

[Install]
WantedBy=multi-user.target
```

然后重启一下服务。
```
systemctl daemon-reload
systemctl start shadowsocks-libev.service
systemctl enable shadowsocks-libev.service
systemctl status shadowsocks-libev.service
```


## 安装 simple-obfs 模块
simple-obfs 是一个混淆模块，这个没有现成的二进制安装包，只能从源码编译了。
```
git clone https://github.com/shadowsocks/simple-obfs.git
cd simple-obfs
git submodule update --init --recursive
./autogen.sh
./configure && make
make install
```


## 配置文件
打开 `/etc/shadowsocks-libev/config.json`，加入 simple-obfs 支持。
```
{
    "server":"IP",
    "server_port":8388,
    "local_port":1080,
    "password":"KEY",
    "timeout":60,
    "method":"chacha20-ietf-poly1305",
    "plugin": "obfs-server",
    "plugin_opts": "obfs=http"
}
```

然后重启 Shadowsocks 服务。
```
systemctl restart shadowsocks-libev.service
```


## 安装锐速
因为锐速对 Linux 内核有要求，这也是我选择 CentOS 而不是 Ubuntu 的原因，CentOS 使用 `kernel-3.10.0-229.1.2.el7.x86_64.rpm`。
```
rpm -ivh http://soft.91yun.org/ISO/Linux/CentOS/kernel/kernel-3.10.0-229.1.2.el7.x86_64.rpm --force
reboot
```

重启过后可以用 `uname -a` 查看当前内核是否为安装的内核，确认无误就可以安装锐速了。
```
wget -N --no-check-certificate https://github.com/91yun/serverspeeder/raw/master/serverspeeder.sh && bash serverspeeder.sh
```


## 防火墙服务
虽然有不少人建议使用 firewalld，不过个人还是习惯 iptables 了，所以就使用它了。
```
yum install iptables-services
iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 8388 -j ACCEPT
iptables-save > /etc/sysconfig/iptables
systemctl restart iptables.service
systemctl enable iptables.service
```


## 客户端使用
客户端其实跟服务端差不多是一样的，也是安装 Shadowsocks-libev 和 simple-obfs，不同的只是服务端用的是 `ss-server` 而客户端用的是 `ss-local`，所以服务文件只需要改一下命令的名字，配置文件如下：
```json
{
        "server":"IP",
        "server_port":8388,
        "local_port":1080,
        "password":"KEY",
        "timeout":600,
        "method":"chacha20-ietf-poly1305",
        "plugin": "obfs-local",
        "plugin_opts": "obfs=http;obfs-host=www.bing.com"
}
```

simple-obfs 有安卓版插件，可以配合 Shadowsocks 客户端使用。
