---
hidden: true
title: 微软虚拟学院在线字幕下载教程
date: 2014-07-06 06:36
tags:
  - 微软虚拟学院
  - Aegisub
  - 字幕
categories:
  - 教程
---

## 前言

**2015.9.29 日已确定此方法失效，MVA 改版后字幕已经不再是文件输出，所以抓不到响应的文件了。**

今天在看 [Programming in C# Jump Start](http://www.microsoftvirtualacademy.com/training-courses/790) 的时候遇到了个有点蛋疼的问题，那个讲师说话的语速太快了，而且咬字不清... 再加上明天就要回去了，家里不一定会有网络，无法在线观看, 网站方有提供视频下载但是没有提供字幕... 为了下载这个名不见经传的字幕，於是开始寻求 Baidu Google 的帮助，不过也没什麽收获，最後只能求助於万能的群友了~

![Microsoft Virtual Academy](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-01.png)

没过多久雪酱(**※ 逝雪-尘封 ※※**)就摸索出来解决方案了，这里是作为笔记备忘，下载完之後的处理由我补充上。


## 准备

  - 浏览器，我这里以 IE11 为例，用来抓取字幕链接用的。
  - IDM 或者其他下载工具，用于下载字幕文件。
  - 记事本或者其他文本编辑器，用来修改字幕文件用。
  - Aegisub，用于生成标准字幕以及修改样式等。
<!--more-->

## 开工

首先打开课程链接，比如[第一课](http://www.microsoftvirtualacademy.com/Content/ViewContent.aspx?et=4226&m=4217&ct=19866)

按 `F12` 打开`开发人员工具`，点击`选择元素(Ctrl+B)`，然後鼠标点击一下播放器窗口。如下图：

找到 `mediacaptions`，右键选择编辑为 HTML。如下图：

![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-02.png)


这时候就可以看到有很多国家的语言了，我们现在只关心`简体中文的`。

![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-03.png)


提取href後的链接，例如`http://channel9.msdn.com/Series/Programming-in-C-Jump-Start/Programming-in-C-01-OOP-Managed-Languages-and-C/captions?f=webvtt&amp;l=zh-cn" rel="webvtt" data-isdefault="false" data-label="简体中文（Simplified)" data-language="zh-cn"`，去掉`amp;`以及`" rel="webvtt" data-isdefault="false" data-label="简体中文（Simplified)" data-language="zh-cn"`，最后只剩下`http://channel9.msdn.com/Series/Programming-in-C-Jump-Start/Programming-in-C-01-OOP-Managed-Languages-and-C/captions?f=webvtt&l=zh-cn`，然後复制到下载工具里下载下来。

![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-04.png)


下载完的是一个 captions 文件，接下来就要开始编辑这个文件，使其成为能被普通播放器识别的标准字幕文件。右键 captions 文件，选择打开方式为`记事本`。
如果有相关字幕处理经验的人一眼就能看出这跟 srt 字幕很像，不同的是 srt 的毫秒是用`小写逗号`分隔的而不是`小写句号`，而且 srt 每一行开头都有序号 (这两个地方感谢[MR菊苣](https://blog.7086.in/#!index.md)点出)。那现在就很简单了

![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-05.png)


光靠简单的记事本是无法批量为其生成序号了，那我们只要利用记事本的批量替换功能把 `.` 全部替换成 `,`，再删掉开头的 WEBVTT 就完成一半了了

![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-06.png)

![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-07.png)


保存后将成品的扩展名改成 srt


但是这样你会发现播放器还是装载不了，因为前面说了这并不是标准 srt 字幕，只是**长得像**，所以我们还是得把它转换成标准字幕。幸运的是 Aegisub 能智能识别这种文件结构，所以只要用 Aegisub 打开这个 srt 文件另存为 ass 文件即可。ASS 的话我们还可以顺便调一下字体丶字号丶颜色丶解析度等参数。

![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-08.png)

![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-09.png)

修改完成后保存为 ass 格式。为了方便，可以把字幕名改成和视频名一样，这样大多数播放器都可以自动加载字幕。

![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-10.png)


OK！到此就全部搞定了~

![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/mva-11.png)
