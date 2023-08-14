---
hidden: true
title: Apache/Nginx 配置 301 重定向
categories:
  - 运维
date: 2015-06-16 05:38:57
tags:
  - Nginx
  - Apache
---

## 前话

相信很多人都有自己的域名吧，不管是作为博客还是其他方便的应用，我们总会希望其他人可以通过 www 或者没有 www 都能访问我们的域名，比如我们访问 `www.example.com` 和 `example.com` 都能访问同一个资源，要实现这样的效果有很多的方法，但是其中最为“有效”的方法则是设置 301 重定向 (301 Redirect)。

但是最好只保留其中的一种，带或者不带 www，因为这样可以更好的做到搜索引擎优化 (SEO)。


## 准备工作

当然了，这个前提你要有自己的服务器，而且要有完全的控制权限，这点应该不用多做解释吧。

其次你的服务器上要有安装 Nginx 或者 Apache，因为本篇文章就是要介绍 Apache/Nginx 下的 301 重定向配置，有了这些准备后继续往下。
<!--more-->

## DNS 解析设置

如何让用户打开 `www.example.com` 和 `example.com` 都能访问到你的资源呢？当然要先把这两个域名都解析到你服务器的 IP 对吧。

这么一来你不管访问有没有带 www 的域名都能解析到你的服务器上，接下去。

## Nginx 配置重定向

为了实现 301 重定向，需要在原有的配置文件里多添加一段代码，比如这是我原来的 Nginx 配置文件
```
server {
	listen 80;
	listen [::]:80;

	root /var/www/html;

	server_name www.example.com example.com;
	index index.html index.htm index.php;

	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php5-fpm.sock;
	}
}
```

我现在要让所有访问带 www 的都重定向到不带 www 的，则需要改成这样
```
server {
    listen 80;
    server_name www.example.com;
    return 301 $scheme://example.com$request_uri;
}

server {
	listen 80;

	root /var/www/html;

	server_name www.example.com example.com;
	index index.html index.htm index.php;

	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php5-fpm.sock;
	}
}
```

如果要让所有访问不带 www 的都重定向到带 www 也是依葫芦画瓢
```
server {
    listen 80;
    server_name example.com;
    return 301 $scheme://www.example.com$request_uri;
}

server {
	listen 80;

	root /var/www/html;

	server_name www.example.com example.com;
	index index.html index.htm;

	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php5-fpm.sock;
	}
}
```

最后不要忘了重新加载一下 Nginx 的配置文件或者重启一下 Nginx 服务
```
nginx -s reload
```
或者
```
service nginx restart
```

**注意：如果是 https 服务器，则将 80 端口改成 443 即可。**

## Apache 配置重定向

### 启用 Rewrite 模块

要使用重定向的功能的话首先得先启动 Apache 的 mod_rewrite 模块，然后重启 Apache 服务
```
a2enmod rewrite
service apache2 restart
```

启用了 Rewrite 模块后就可以愉快的使用 `.htaccess` 文件来设置 Rewrite 规则了，使用过 WordPress 之类的 CMS 系统的朋友们是不是很眼熟呢？

### 启用 .htaccess 文件

打开 Apache 的配置文件，在原有配置文件基础上加入
```
<Directory /var/www/html>
	Options Indexes FollowSymLinks MultiViews
	AllowOverride All
	Order allow,deny
	allow from all
</Directory>
```

其中 `/var/www/html` 换成你的实际路径，也就是你在配置文件中设置的 `DocumentRoot`。

保存并重启 Apache 服务~
```
service apache2 restart
```

### 配置 Rewrite 模块

切换到文档根目录，创建一个 `.htaccess` 文件，然后用 Vim 之类的编辑器打开
```
cd /var/www/html
touch .htaccess
vim .htaccess
```

如果要让所有访问带 www 的都重定向到不带 www 的，则往 `.htaccess` 文件中写入
```
RewriteEngine On
RewriteBase /
RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
RewriteRule ^(.*)$ http://%1/$1 [R=301,L]
```

如果要让所有访问不带 www 的都重定向到带 www 的，则改成
```
RewriteEngine On
RewriteBase /
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteRule ^(.*)$ http://www.%{HTTP_HOST}/$1 [R=301,L]
```

最后重启一下 Apache 服务就可以了
```
service apache2 restart
```

**同样的，如果你使用的是 https 服务器，则只需要将上述的 Rewrite 规则中的 http 改成 https 即可。**

## 测试

使用 curl 命令测试一下重定向是否生效

```
curl -I http://example.com
```

假设我们是把所有不带 www 的都重定向到带 www 了，则返回的结果应该类似于
```
HTTP/1.1 301 Moved Permanently
Server: nginx/1.6.2
Date: Tue, 16 Jun 2015 09:36:09 GMT
Content-Type: text/html
Content-Length: 193
Connection: keep-alive
Location: http://www.example.com/
```

重点的是 **301 Moved Permanently** 以及下方的 **Location**。

OK！导戏结束，欢迎吐槽！
