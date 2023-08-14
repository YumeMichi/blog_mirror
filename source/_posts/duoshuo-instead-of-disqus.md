---
hidden: true
title: 放弃 Disqus 启用多说
date: 2015-09-27 16:24:33
tags:
  - 日记
categories:
  - 日记
---

由于墙的缘故，博客从今天起将放弃使用 Disqus 转而使用多说，原有的评论数据已经导出，明天再琢磨一下怎么转成多说能解析的 JSON 格式~

在 GitHub 上找到了一个将 Disqus 评论转成多说评论的小工具 [disqus2duoshuo.js](https://github.com/jsw0528/disqus2duoshuo.js)，但是已经很久没更新了。多说现在的 `thread` 下的 `thread_key` 参数已经不再是原来的 Disqus 的 `dsq:id` 了，同样 `post` 下的 `thread_key` 也不是了。

`thread` 对应的 `thread_key` 变成了去除 FQDN 后剩余部分再加上 `index.html`，比如我的关于页面的链接是 `https://blog.ikke.moe/about/`，那么需要生成的多说能解析的 `thread_key` 就是 `/about/index.html`，如果是 `post` 文章页面的话，`thread_key` 将会变成去除 FQDN 后剩余的部分，例如本文的连接是 `https://blog.ikke.moe/posts/duoshuo-instead-of-disqus/`，那么需要生成多说能解析的 `thread_key` 将会是 `/posts/duoshuo-instead-of-disqus/`。

很残念的是我不会 Node.js，事实上 JavaScript 我也只会点皮毛而已，所以担不起重写代码的责任，我打算在上面开个 issue 让原作者自己更新吧~~
