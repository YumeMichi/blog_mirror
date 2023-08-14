---
title: ArchLinux 安装备忘：续
date: 2015-12-08 13:12:32
tags:
  - Notes
  - ArchLinux
categories: Linux
---

## 续前话

在虚拟机里调试了几天，终于鼓起勇气往实体机安装了，到桌面环境为止的安装过程可以看我的前一篇文章[《ArchLinux 安装备忘》](https://blog.ikke.moe/posts/archlinux-installation-notes/)。桌面环境我使用的是 GNOME，虽然用了很长一段时间的 KDE，但是 KDE5 神一般的开机速度简直让人喜感，最后还是选择了 GNOME～

## 安装桌面环境

### 安装 Xorg
原本以为需要安装整个 Xorg 事实上根本不用～
```
sudo pacman -Syu
sudo pacman -S xorg-xinit xorg-server xorg-twm xterm
```

桌面环境可根据需要安装 GNOME 或者 KDE。

<!--more-->

### 安装 GNOME
```
sudo pacman -S gnome
```
如果你还需要 GNOME 自带的软件的话
```
sudo pacman -S gnome-extra
```
顺便安装一下 GNOME 优化工具 GNOME Tweak Tool
```
sudo pacman -S gnome-tweak-tool
```
显示管理器 GNOME 默认是用的是 GDM。
```
sudo systemctl enable gdm.service
```
装完 GNOME 进系统过后你会发现没地方设置网络，到 Settings 里面找到 Network 点开会提示你 NetworkManager 没有运行。
```
sudo systemctl start NetworkManager.service
sudo systemctl enable NetworkManager.service
```
然后重启电脑应该就能进入 GDM 了，选择用户名密码登录即可。

### 更新系统
因为我们在大天朝，所以软件源建议设置为国内的，例如清华大学 TUNA 或者中科大 USTC。
注意备份已有的 mirrorlist 文件。
```
sudo mv /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak
sudo echo Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch > /etc/pacman.d/mirrorlist
sudo pacman -Syy
sudo pacman -Syu
```

### 安装 Yaourt
同样使用 TUNA 的镜像，编辑 `/etc/pacman.conf` 文件，加入
```
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

导入 GPG Key。
```
sudo pacman -S archlinuxcn-keyring
```

然后执行 以下命令安装 Yaourt
```
sudo pacman -Syu
sudo pacman -S yaourt
```

## 中文化

由于本人习惯使用简体中文，所以自然要切回简体中文。

### 设置 Locale

编辑 `/etc/locale.gen` 文件，去掉 `zh_CN.UTF-8` 前面的注释，然后执行 `sudo locale-gen`。

### 设置系统语言

到系统设置地域语言中，把语言改成 **汉语(中国)**，然后注销用户重新登录即可。

### 优化中文显示

重启后你会发现中文显示很难看还锯齿严重，因为系统并没有安装合适的中文字体以及开启抗锯齿。

中文字体推荐[思源黑体（Source Han Sans）](https://github.com/adobe-fonts/source-han-sans)，我真的是非常喜欢哈哈～
```
sudo pacman -S adobe-source-han-sans-cn-fonts
```

装完中文字体后打开 GNOME Tweak Tool，中文名叫做优化工具，切换到字体一栏，将窗口、界面、文档的字体改为 `Source Han Sans Normal`，中文叫做 **思源黑体**，重口味的需要窗口用粗体的话也自行更改，然后别忘了将下方的抗锯齿选择为 `Grayscale` 或者 `Rgba`，到此中文化就完整了，看着界面真是舒服。

另外还有个等宽字体的设置，个人用的是 [Source Code Pro](https://github.com/adobe-fonts/source-code-pro/)，另外再推荐一个 Mac 用户必定知道的字体 [Monaco](https://github.com/cstrap/monaco-font)。

## 安装常用软件

### 火狐浏览器
```
sudo pacman -S firefox firefox-i18n-zh-cn
```

### 谷歌浏览器

ArchLinux 官方并没有收录谷歌浏览器，安装包来自于前面添加的 ArchLinuxCN 源。
```
sudo pacman -S google-chrome
```

### 搜狗输入法

用了几个输入法还是习惯搜狗，你呢？
```
sudo pacman -S fcitx-im fcitx-configtool fcitx-gtk3 fcitx-gtk2 fcitx-qt4 fcitx-qt5 fcitx-sogoupinyin
```

### 腾讯 QQ

表示笔者还真离不开 QQ，不是我不想离开而是离开了 QQ 就脱离了一大帮交际圈……

安装 QQ 的话有几个选择，一个是使用最新的 QQ 轻聊版自己制作，另外一个就是使用 Deepin 制作的 QQ 国际版，笔者自己动手搞了一把轻聊版，然而稳定性实在令人堪忧，老是程序自动关闭，完全没法用，所以还是选择了后者，虽然版本旧了点但是稳定性好。
```
yaourt wine-qqintl
```
注意的是 AUR 上面的 QQ 国际版其中的下载链接没有及时更新，所以 Yaourt 的时候要编辑 PKGBUILD 文件，将里面的下载链接换成 http://packages.linuxdeepin.com/enterprise/pool/non-free/d/deepinwine-qqintl/wine-qqintl_0.1.3-2_i386.deb 即可。

### Shadowsocks-Qt5
一个 Shadowsocks 图形化管理软件，崩溃率略高，所以我已经用回 shadowsocks-python 了～
```
yaourt shadowsocks-qt5
```
安装完需要配置一个 Shadowsocks 账号，你懂的～

### VPN 扩展
```
sudo pacman -S networkmanager-pptp
yaourt networkmanager-l2tp
sudo systemctl restart NetworkManager
```

### 读写 NTFS 格式

装完 ArchLinux 后对 NTFS 格式的硬盘只有读取权限，需要安装 `ntfs-3g` 后才能正常写入。
```
sudo pacman -S ntfs-3g
```

### 解压缩软件
```
sudo pacman -S file-roller unrar unzip p7zip
```

### 文本编辑器
gedit 和 vim
```
sudo pacman -S gedit vim
```

有需要的话还有 Sublime Text 和 Atom
```
sudo pacman -S atom sublime-text-imfix
```

### ProxyChains
非常无比强大的终端代理工具，配合 SS 简直超神！！！
```
sudo pacman -S proxychains
```
安装完后编辑 `/etc/proxychains.conf` 文件，将最后一行的代理地址填写成你实际的，比如我用的 Shadowsocks 的话就是 `socks5 127.0.0.1 1080`，使用的话就是在命令前加上 proxychains 比如 `proxychains wget http://xxx.com/xxx.avi` 即可～

### RP-PPPOE
这个不一定所有人都用得到，拨号连接用的，笔者宿舍没有自己装路由器，所以每次上网都要输入手机号和密码，就需要安装这个软件。
```
sudo pacman -S rp-pppoe
nm-connection-editor
```
打开网络配置工具添加一个 DSL 连接，输入用户名密码保存即可。

### SSH、Git
应该说这几个软件已经是使用 Linux 都必备的了。
```
sudo pacman -S git openssh
```

### 影音娱乐
音乐播放器、视频播放器等等，大名鼎鼎的 MPlayer、VLC、Audacious 等等…
```
sudo pacman -S vlc mplayer smplayer audacious
```

### VMware 虚拟机

参考[官方文档](https://wiki.archlinux.org/index.php/VMware)

#### 下载 VMware
```
wget https://download3.vmware.com/software/wkst/file/VMware-Workstation-Full-12.5.2-4638234.x86_64.bundle
chmod +x VMware-Workstation-Full-12.5.2-4638234.x86_64.bundle
```

#### 安装必要依赖
```
sudo mkdir /etc/init.d
sudo pacman -S fuse gtkmm linux-headers ncurses5-compat-libs
```

#### 安装 VMware
```
sudo ./VMware-Workstation-Full-12.5.2-4638234.x86_64.bundle
```

#### 添加 VMware 服务配置文件
```
yaourt vmware-systemd-services
sudo systemctl start vmware.service
sudo systemctl enable vmware.service
```

### WPS
```
sudo pacman -S wps-office
```

### Steam 平台

文章发表于将近一年前了，等我最近测试一下再更新此部分！
