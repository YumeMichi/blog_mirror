---
hidden: true
title: MIUI7 安装 Google Apps
date: 2015-09-29 17:00:13
tags:
  - Android
  - 小米
  - Google
categories:
  - 手机
---

![MIUI7](/img/miui7.png)

升级 MIUI7有几天了，体验上感觉还可以，耗电问题感觉改善并不是很明显，很多人甚至觉得更耗电了也是醉了。至于发热嘛，只能说偶尔吧，但是发起热来真是可怕，赶紧重启。。。

好了废话不多说了，因为要玩 LoveLive! 日服的缘故，需要安装谷歌服务框架，但是我用的是官方版的 MIUI7 也就是线刷的版本，用过官方 MIUI7 的人应该都知道只能用自带的 recovery，像 CWM 和 TWRP 之类的第三方 recovery 不能刷，刷了就进不了系统了，所以 gapps 卡刷包就用不了了，但是我又不知道究竟游戏运行需要的谷歌服务框架都有哪些软件。
<!--more-->

以前在 MIUI5 的时候小米应用商店里有个谷歌安装器，用来安装谷歌应用程序的，挺方便的。但是由于谷歌方的版权问题软件已经下架好久了，网上找的也基本牛头不对马嘴，MIUI5 是 Android 4.x 来着，MIUI7 已经是 Android 5.0.2 了，装上去的软件直接就闪退。

我赵日天不服，在谷歌上 Google 了一下 `install gapps manually` 也就是手动安装 gapps，与以往卡刷相对而言。但是结果总是不尽人意，只找到了一个比较符合我预期搜索的帖子 [MIUI 6 ROM manually install Play Store app / Lite GAPPS](http://en.miui.com/thread-89213-1-1.html)，看内容应该是发帖的人自己装了几个必要的组件然后就可以了，于是我就想说去下个 gapps 卡刷包然后在里面找一下对应的这些包。

于是乎我又去 xda 上面找了一下最新的 Android 5.0.2 用的 gapps，也就是这个 [[GAPPS][2015-04-04] Google Apps Minimal Edition for Android 5.0.x & 5.1.x](http://forum.xda-developers.com/android/software/gapps-google-apps-minimal-edition-t2943330)。下完后展开包... 好吧，这么多个 gms 让我选哪个是好，而且貌似这里面的 gms 的 lib 还是单独置于包外的... 好吧，这实在是超出我的个人能力了，还是乖乖找个安装器吧。

![](/img/gms-1.png)

![](/img/gms-2.png)

抱着试一试的心态回到小米应用商店，搜索了一下**谷歌**二字，果然是因为版权问题不能上架谷歌相关的应用了，但是下面有个很热心的提醒，可以在百度上搜索一下。

![小米应用商店](/img/baidu-google-installer.png)

好啊，那就试试，于是就有了我现在用的这个谷歌应用安装器。

![谷歌安装器](/img/mi-shop.png)

非常暴力，直接一键安装即可。需要翻墙哈，因为是从谷歌服务器下载的对应软件，没梯子的话是不行滴~

![谷歌安装器](/img/google-installer.png)

所以我废话了半天其实就是上百度应用下一个他提供的谷歌安装器然后他就会自动帮你下载所需要的几个软件，手动安装完后就可以直接使用 Google Play 啦~

![Google Play](/img/google-play.png)

最后：LL 大法好！！！

![LL 大法好](/img/lovelive.png)
