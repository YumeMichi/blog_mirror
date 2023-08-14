---
hidden: true
title: WebStorm 11 破解方法
date: 2015-12-03 17:43:42
tags:
  - WebStorm
  - 破解
categories:
  - 软件
---

用过前面几个 WebStorm 或者 JetBrains 其他几个产品的朋友们应该都很清楚，以前要破解 WebStorm 很简单只需要随便网上找个 Key 就能激活了，但是自从 JetBrains 产品全线升级到 11 后，已经找不到任何 Key 或者 Keygen(注册机) 了，好像是因为算法改变了！

但是像我这种穷逼学生想用却买不起咋办，百度无果转寻谷歌，意外的发现了[这篇文章](http://www.rover12421.com/2015/04/07/jetbrains-product-patch-or-keygen.html)，作者简直厉害的不要不要的～！！！

想要理解原理的可以细读一下他的文章，如果只是为了激活产品的，只需要下载破解文件即可。

下载地址：http://pan.baidu.com/s/1gdw7T0v
<!--more-->

下载 **`JetBrainsCrackV2.1.zip`** 这个文件，然后解压开找到 `JetbrainsCrack.jar` 文件，拷贝到 WebStorm 安装目录下的 `bin` 目录下。

接下来用文本编辑器打开 `webstorm64.vmoptions` 文件，在末尾添加 `-javaagent:JetbrainsCrack.jar`，保存退出。

启动程序，在序列号输入框中粘贴以下代码即可。
```
ThisCrackLicenseId-{
"licenseId":"ThisCrackLicenseId",
"licenseeName":"Rover12421",
"assigneeName":"",
"assigneeEmail":"rover12421@163.com",
"licenseRestriction":"For Rover12421 Crack, Only Test! Please support genuine!!!",
"checkConcurrentUse":false,
"products":[
{"code":"II","paidUpTo":"2099-12-31"},
{"code":"DM","paidUpTo":"2099-12-31"},
{"code":"AC","paidUpTo":"2099-12-31"},
{"code":"RS0","paidUpTo":"2099-12-31"},
{"code":"WS","paidUpTo":"2099-12-31"},
{"code":"DPN","paidUpTo":"2099-12-31"},
{"code":"RC","paidUpTo":"2099-12-31"},
{"code":"PS","paidUpTo":"2099-12-31"},
{"code":"DC","paidUpTo":"2099-12-31"},
{"code":"RM","paidUpTo":"2099-12-31"},
{"code":"CL","paidUpTo":"2099-12-31"},
{"code":"PC","paidUpTo":"2099-12-31"}
],
"hash":"2911276/0",
"gracePeriodDays":7,
"autoProlongated":false}
```
到此就破解完毕了。

另外如果是 Linux 用户，WebStorm 是解压版的，第一次运行的时候会帮你在应用程序启动器(开始菜单)中添加 WebStorm 的快捷方式，但是运行这个快捷方式的时候你会发现运行不了。

打开快捷方式的设置可以看到其实运行的就是 `webstorm.sh` 这个文件，那跟直接双击这个 Shell 脚本有啥不同？

环境变量不同，直接复制快捷方式里的命令到 Terminal 执行一下，会提示找不到 `JetBrainsCrack.jar`，只需要在 `webstorm64.vmoptions` 写上该文件的绝对路径即可。
```
-javaagent:/home/ikke/WebStorm-143.382.36/bin/JetbrainsCrack.jar
```

另外此方法适用于 JetBrains 最新全线产品，只有需要修改的 `vmoptions` 文件不同而已。

**最后再次感谢原作者：Rover12421**
