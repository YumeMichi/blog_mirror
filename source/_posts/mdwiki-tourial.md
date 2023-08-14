---
hidden: true
title: MDwiki 搭建静态博客教程
date: 2015-10-12 04:24:34
tags:
  - MDwiki
  - Hexo
  - Markdown
categories:
  - 教程
---

## MDwiki 快速搭建

### 引言

MDWiki —— 基于 Markdown 和 HTML 5 的纯客户端 Wiki/CMS

### 简介

[MDwiki](http://dynalon.github.io/mdwiki/#!index.md) 是一个**完全使用 HTML5/Javascript 技术构建，完全运行在客户端**的 Wiki/CMS 系统。无需专门的服务器软件，只需将 `mdwiki.html` 上传到你存放 markdown 文件的目录。

### 特性

  - 完全使用 HTML5/Javascript 构建，无需在本地或远程安装任何软件。
  - 使用 Markdown 。
  - 基于 jQuery 和 Bootstrap 3 的响应式布局。
  - 增强的 Markdown 语法，例如代码高亮、GitHub Gists、Google 地图。
  - 可以修改主题。支持所有 [bootswatch](http://www.bootswatch.com/) 的主题。

<!--more-->

### 需求

  - 网页空间（或者支持静态文件的 Web 服务器）
  - 任何一个现代的浏览器
  - `mdwiki.html` 文件

### 工作过程

在开始 Markdown 之前先来介绍一下 mdwiki.html 的原理。

  - 首先我们将 mdwiki.html 文件和你的 md 文件放在同一目录下
  - 然后解析的时候就通过 `#!`，例如 http://localhost:8080/mdwiki.html#!example.md
  - 由于大多数的 Web 服务器都会自动识别诸如 index.html、index.php 文件，所以如果将 mdwiki.html 重命名为 index.html，在多数 Web 服务器下都可以省略 index.html 而直接使用 http://localhost:8080/#!example.md

这也正是一开始就推荐用 index.html 的原因，但是原理肯定还是要说明一下滴。


## 安装环境

本文以 Linux 系统为例，Windows 以及 Mac 的话过程也是差不多的。

首先得有一个网页空间，这里先以本地硬盘作为网页空间。要访问本地文件，首先得有个 Web 服务器，由于 Apache、Nginx 之类的 Web 服务器配置较多而且暂时不需要用到，所以就以 npm 的 http-server 作为本文的 Web 服务器。

```
## 首先安装 nvm 这个 Node.js 版本控制工具
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.29.0/install.sh | bash

## 安装 Node.js (需要哪些版本可以使用 nvm ls-remote 查看所有版本)
nvm install v0.12.7

## 安装 http-server
npm install http-server -g
```

MDwiki 的原理事实上就是使用一个 HTML 文件来实时解析用户自己编写的 md 文件，从而免去了将 md 文件编译成 html 静态文件的过程。所以接下来就是下载**[这个 Markdown 文件](https://github.com/Dynalon/mdwiki/releases)**了。

```
# 创建博客主目录
mkdir blog

# 下载已编译好的 mdwiki
wget https://github.com/Dynalon/mdwiki/releases/download/0.6.2/mdwiki-0.6.2.zip

# 解压
unzip -d mdwiki mdwiki-0.6.2.zip

# 复制 mdwiki.html 文件并改名为 index.html 放到博客主目录下
cp mdwiki/mdwiki-0.6.2/mdwiki.html blog/index.html

# 启动 http 服务
cd blog
http-server
```

一般来说没什么问题的话都会提示 http-server 已启动

![](/img/http-server.png)

按照提示的 IP 地址访问即可。

![](/img/mdwiki-startpage.png)

## Markdown 快速入门

由于 MDwiki 使用的是纯 Markdown 语言，所以你必须得先会点 [Markdown 语言基础](#)哈。

这部分的语法介绍基本上翻译自官方文档，英文好的同学可以自己啃[英文版](http://dynalon.github.io/mdwiki/#!quickstart.md)哈。

### 基本框架

一个简单的 Markdown 文件看起来应该像
```markdown
标题
====

子标题
------

  * 无序列表项目 1
  - 无序列表项目 2
  1. 有序列表项目 1
  2. 有序列表项目 2

超级链接 [Google](http://google.com)

图片链接看起来跟超级链接类似，只是前面多了个感叹号：
![](https://raw.githubusercontent.com/YumeMichi/hexo-idsy/gh-pages/img/blog.png)
```

  - 主标题下方使用 `=` 标记
  - 子标题下方使用 `-` 标记
  - 无序列表使用 `*` 或者 `-` 标记
  - 超级链接使用 `[Description](URL)`，其中 URL 可以是网络资源也可以是本地资源。
  - 图片链接使用 `![Description](URL)`，其中 URL 可以是网络资源也可以是本地资源。
  - 注意缩进和换行，这是个好习惯，某些时候却是必须严格遵守的！

MDwiki 使用了 [GitHub flavored markdown dialect](https://help.github.com/articles/github-flavored-markdown/)。所以如果有需要，你可以这样创建一个表格

```markdown
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |
```

  - `-----` 代表默认的左对齐，等同于 `:-----`
  - `:-----:` 代表居中对齐
  - `-----:` 代表右对齐

一起看个完整的实例
```markdown
MDwiki测试页面
==============

列表
----

  * 无序 1
  - 无序 2
    1. 嵌套有序 1
    2. 嵌套有序 2
    3. 嵌套有序 3

超链接
------
  [百度](http://www.baidu.com)
  [谷歌](http://www.google.com)

图片
----
  ![网络图片](https://blog.ikke.moe/img/miui7.png)
  ![本地图片](/img/miui7.png)

表格
----
  | AAAAA | BBBBB | CCCCC |
  | ----- |:-----:| -----:|
  | R1    | R2    | R3    |
  | S1    | S2    | S3    |
```

先在脑海里想象一下样子，然后打开[预览](http://yumemichi.github.io/#!demo1.md)看看！怎么样，跟你想象的是一样的吗？

现在我们的页面长什么样子？

![No Navigation](/img/no-navigation.png)

### 导航栏实现

接下来添加点新东西 —— 导航栏

编辑 `navigation.md` 文件
```markdown
[Home](index.md)
[About](about.md)
```

现在我们就有了一个简单的导航栏了~ 需要其他的栏目也是按照一样的语法添加即可。

![Navigation](/img/navigation.png)

接下来再来点高级点的 —— 二级菜单

```markdown
# YumeMichi's WiKi

[Tourials]()

  * # Linux
  * [Post 1](post1.md)
  * [Post 2](post2.md)
  - - - -
  * # Windows
  * [Post 3](post3.md)
  - - - -
  * # Mac
  * [Post 4](post4.md)

[About](about.md)
- - - -
[Links](links.md)
- - - -
[gimmick:themechooser](Theme)
- - - - 
[gimmick:theme](cosmo)
```

可以看出 Tourials 是一个二级下拉菜单，它包括 Linux、Windows 和 Mac 三个子菜单，子菜单又分别列出了相应的文章目录。

  - 子菜单名称前的 # 是必须的，* 代表了这个菜单是无序的，即无编号。
  - 子菜单之间的分隔 - - - - 也是必须的。
  - 主菜单之间的分隔 - - - - 不是必须的，但是建议加上。
  - gimmick:themechooser 是一个主题选择器。
  - gimmick:theme 则选择指定的主题。

这些都是可以在导航栏里实现的。

![Anvanced Navigation](/img/advanced-navigation.png)

### 超链接使用

在上面我们稍微接触到了超链接，这里另外详细说明一下超链接的用法。

第一种，比如要设置一个超链接到 `https://google.com/`，只需要使用 `[Google](https://google.com)`。
第二种，比如要设置一个超链接到博客本身的某个文件，例如 `~/blog/demo1.md`。还记得前面说过的吗，解析 md 文件需要用到 `#!`，但是如果是在超链接中则可以省略 `#!`，直接使用 `[Demo 1](demo1.md)`。

### 图片使用

图片的基本用法是 `![alt](href "title")`，其中 alt 用于当图片无法显示的时候替代图片位置上显示的文字提示
title 用于当鼠标移动到图片上的时候显示的提示信息。
```markdown
![图片 1](https://blog.ikke.moe/img/miui7.png "网络图片")
![图片 2](img/miui7.png "本地图片")
![图片 3](img/unknown.png "图裂了")
```

[点我预览](http://yumemichi.github.io/#!demo2.md)

### 图片和超链接嵌套使用

有时候我们需要点击一张图片的时候不是显示图片而是跳转到某个链接去，这时候我们就可以嵌套使用超链接和图片了。

```markdown
[![My Blog](https://blog.ikke.moe/img/favicon.ico)](https://blog.ikke.moe)
```

[点我预览](http://yumemichi.github.io/#!demo3.md) 就是这么简单！

### 代码高亮

这部分应该是各位博友们最经常用到的功能之一了，Markdown 的语法高亮也是非常的方便的！

代码高亮有两种形式：一种是单行，一种是多行。

单行比较简单，仅需要用使反引号 (键盘上数字1左边，Tab 键上方，需要在英文输入模式下) 包围在需要高亮的代码前后即可。

多行也是很简单，只是弥补了单行的不足，比如支持多行、支持针对语言！

基本用法则是在代码前后使用3个反引号，比如前面贴出的各段代码。

若要支持特定语言的语法，则可加上该语言的名称，例如
```javascript
var hello = function () {
    // say hello
    alert('Hello world!');
}
```

Markdown 支持的语言列表

| 语言 | 关键字 |
| --- | ----- |
| Bash | bash |
| C# | csharp |
| Clojure	| clojure |
| C++	| cpp |
| CSS	| css |
| CoffeeScript	| coffeescript |
| CMake	| cmake |
| HTML	| html |
| HTTP	| http |
| Java	| java |
| JavaScript	| javascript |
| JSON	| json |
| Markdown	| markdown |
| Objective C	| objectivec |
| Perl	| perl |
| PHP	| php |
| Python	| python |
| Ruby	| ruby |
| R	| r |
| SQL	| sql |
| Scala	| scala |
| Vala	| vala |
| XML	| xml |

## 页面布局

前面我们已经大致了解了撰写一篇 Markdown 文章所需要的基本语法了，这一部分我将介绍一些关于布局的问题。

总体布局在我们前面的 demo1 已经介绍过了，主要就是像这样
```markdown
主标题
======

分级标题 1
----------
内容

分级标题 2
----------
内容

分级标题 3
----------
内容
```

这里主要要介绍的是图文混排，图片与图片、图片与文本之间的位置关系。

### 图片居于文字左边

将图片的链接放置在段落文本的正上方，中间不加换行。
```markdown
![](https://blog.ikke.moe/img/miui7.png)
这里是段落内容，注意两行中间并没有任何的换行！
```

### 图片居于文字右边

将图片的链接放置在段落文本的正下方，中间不加换行。
```markdown
这里是段落内容，注意两行中间并没有任何的换行！
![](https://blog.ikke.moe/img/miui7.png)
```

### 图片与文字无关

只需要在文字与图片之间加上换行即可。
```markdown
![](https://blog.ikke.moe/img/miui7.png)

这里是段落内容，注意两行中间有换行！
```

### 图片并排

两张图片的链接并列，中间没有换行。
```markdown
![](https://blog.ikke.moe/img/miui7.png)
![](https://blog.ikke.moe/img/miui7.png)
```

### 组合效果

借助上面的几种图文混排效果我们还可以实现组合效果，例如图片并排然后居于段落文字左边。
```markdown
![](https://blog.ikke.moe/img/miui7.png)
![](https://blog.ikke.moe/img/miui7.png)
这里是段落内容，注意两行中间并没有任何的换行！
```

[点我预览效果](http://yumemichi.github.io/#!demo6.md)

## 自定义配置

自定义配置主要是在一些小细节上作调整，由于官方目前开放的配置参数还太少，所以本文也就不做太细致的介绍了，只说一下笔者用到的几个。

### 主题选择器

这个东西在前面的导航栏介绍里有说到过了，引用官方的说法这是一个特殊的花招 (gimmick)，要启用它可以在 navigation.md 里加上。
```markdown
[gimmick:themechooser](Theme)
```

而与主题选择器相对应的自然就是选择一个主题，注意的是这里指的是选择一个默认主题，MDwiki 默认的主题是 bootstrap，我们可以修改成其他的。
```markdown
[gimmick:theme](flatly)
```

需要注意的是，只有默认的 bootstrap 主题支持离线，也就是说其他的主题都需要联网才能使用，所支持的主题可以看主题选择器里列出的。
要启用此参数一样也是在 navigation.md 里加上。

### 配置文件

这是一个官方开放的可由用户自定义的配置文件(虽然现在开放的参数少得可怜)，新建一个 config.json 文件与 mdwiki.html 文件放置在同一文件夹下。

一个简单的 config.json 文件应该像这样
```json
{
    "useSideMenu": true,
    "additionalFooterText": "© 2015 YumeMichi ",
    "title": "YumeMichi's MDwiki Demo Page",
    "anchorCharacter": "#"
}
```

  - `"useSideMenu": true` - 启用侧面的导航栏，也就是页面左边的导航栏。
  - `additionalFooterText: ""` - 可以用来在页面底部添加版权信息等其他文字信息，默认为空。
  - `"title": ""` - 添加选项卡栏处现实的网页标题，这个参数官方是没有开放的，感谢**MR菊苣**提供！
  - `"anchorCharacter": "#"` - 当鼠标移动到分级标题上显示的一个图标，笔者也不知道是干嘛的，默认是`¶`。

### 主页设计

到此，MDwiki 就只剩下主页了，主页就相对简单了，就是利用我们前面所学的设计出一个主页，命名为 index.md，以下是我的 demo 主页
```markdown
YumeMichi's Wiki
================

Welcome
------
Welcome to my mdwiki-demo page, I am YumeMichi, an Anime and Music lover!

Demo
-------
  - [demo1](demo1.md)
  - [demo2](demo2.md)
  - [demo3](demo3.md)
  - [demo4](demo4.md)
  - [demo5](demo5.md)
  - [demo6](demo6.md)

Links
-----
 - [MDwiki](http://dynalon.github.io/mdwiki/)
 - [CommonMark Spec](http://jgm.github.io/stmd/spec.html)
 - [My Blog](https://blog.ikke.moe)
```

## 结尾

不知道你是否已经掌握了！接下去的部分将会开始介绍如何将博客发布出去，因为到目前为止我们事实上都还是在本机上预览的，要给别人看肯定要发布出去的，后续部分将介绍将代码托管到 GitHub 之类的代码托管网站和 Linux 服务器上，读者最好是有相应的基础，尤其是 Linux 篇，当然如果没有需要的话大可直接略过，托管到 GitHub 已经是非常方便了！
