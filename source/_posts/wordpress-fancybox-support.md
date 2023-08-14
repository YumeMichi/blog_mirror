---
hidden: true
title: WordPress 增加 fancybox 支持
date: 2015-10-30 21:48:38
tags:
  - WordPress
  - fancybox
categories:
  - 博客
---

## 前话
用了好长时间的 Hexo 突然莫名的想念 WordPress，或许是因为对天天在终端上码文章感到疲倦了，就在昨天，我又装回 WordPress 了。

Hexo 的主题基本都会带一个神奇的小玩意儿 —— fancybox。
![fancobox](/img/fancybox.png)

<!--more-->

换回 WordPress 后还真不习惯没有 fancybox，于是乎开始鼓捣怎么让 WordPress 支持 fancybox。翻了官网的 [How to use](http://www.fancybox.net/howto) 以及在搜索引擎上查阅了一下，发现了一篇写的挺完整地就搬过来了[《WordPress非插件使用fancybox》](http://blog.sina.com.cn/s/blog_65429b3d0101k5ho.html)，官网主要是没有针对使用平台作介绍，只是讲了如何使用。

## 处理图片
因为 fancybox 是 jQuery 插件，使用中需要jQuery 选择器选择图片元素，所以需要对所有的图片做一定的处理，以达到选择器能够拾取到。
```
1. First, make sure you are using valid DOCTYPE
2. Include necessary JS files
3. Add FancyBox CSS file
4. Create a link element (<a href>)
5. Fire plugin using jQuery selector
```
这是官网提供的使用步骤，介于我下载了源码包，所以相当于直接跳到第四步了。批量为图片包裹上 `a` 标签，这个最方便的自然还是使用 JavaScript 了

将以下代码添加到 `header.php` 或者 `footer.php`，我是添加到 `footer.php` 了
```javascript
<!--图片链接到原图-->
<script type="text/javascript">  
$(function() {  
    $('.entry-content img').each(function(i){  
        if (! this.parentNode.href) {  
            $(this).wrap("<a href='"+this.src+"'></a>");  
        }  
    });  
});  
</script>
```
![](/img/footer.php.jpg)

接下来下载最新版的 fancybox，下载链接：[https://github.com/fancyapps/fancyBox/zipball/v2.1.5](https://github.com/fancyapps/fancyBox/zipball/v2.1.5)，解压开得到 `source` 目录并复制到网站目录下的任意目录内，比如我就直接放在网站根目录下了。
```bash
wget --content-disposition https://codeload.github.com/fancyapps/fancyBox/legacy.zip/v2.1.5
unzip -d . fancyapps-fancyBox-v2.1.5-0-ge2248f4.zip
cp -R fancyapps-fancyBox-18d1712/source /var/www/wordpress/fancybox
```

接下来往 `function.php` 加入以下代码，以便于 jQuery 拾取。
```php
//fancybox 自动对图片链接添加rel=fancybox属性
add_filter('the_content', 'pirobox_gall_replace');
function pirobox_gall_replace ($content){
    global $post;
    $pattern = "/<a(.*?)href=('|\")([^>]*).(bmp|gif|jpeg|jpg|png)('|\")(.*?)>(.*?)<\/a>/i";
    $replacement = '<a$1href=$2$3.$4$5 rel="fancybox"$6>$7</a>';
    $content = preg_replace($pattern, $replacement, $content);
    return $content;
}
```

回到 `footer.php` 添加以下代码，其中文件路径改成自己实际的路径，以网站根目录为基础。
```javascript
<link rel="stylesheet" type="text/css" href="/fancybox/jquery.fancybox.css" />
<script src="/fancybox/jquery.fancybox.pack.js"></script>
<script type="text/javascript">
$(function() {
    jQuery("a:has(img)").attr({rel: "fancybox"});
    jQuery("a[rel=fancybox]").fancybox();
});
</script>
```
![](/img/footer-2.php.jpg)

这样就可以啦！另外如果你的主题没有使用到 jQuery 的话就在 `header.php` 里添加以下引用 jQuery，在线的活着本地均可，只要是在上面那段引用 fancybox 的代码之前均可。jQuery 下载地址：[http://code.jquery.com/jquery-1.11.3.js](http://code.jquery.com/jquery-1.11.3.js)

效果演示：直接点一下本文中的图片就知道了～
