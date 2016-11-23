---
title: ArchLinux 配置 LANMP 环境
date: 2016-05-19 15:06:28
tags:
  - Nginx
  - Apache
  - MySQL
  - PHP
  - ArchLinux
categories: Linux
---

## 前言

昨天不知道怎么回事的，系统出了问题，就是终端无法使用，退格键会被当成空格，Ctrl+L快捷键原本是清理屏幕的结果每按一次，就会多出一个 @Yume（我的 hostname），在群里闻了一下也没人知道是为什么，想谷歌但是又不知道要用什么关键字…… 所以，最后还是重装系统了。

还是一样需要搭建 PHP 运行环境，前天吃了亏所以我今天直接使用了 PHP 5.6，具体情况可以看前天的 ArchLinux 下 ThinkPHP 问题整理，配置好后开始部署项目，打开 localhost 输入用户名密码，登录…… 咦，登录不上？好吧，数据库配置项忘了去掉密码了，因为我本机数据库没有设置密码。再来一次 localhost，还是登录不了……

<!--more-->

我开始方了…… 难道是 PHP 环境有问题，跟前天一样，MySQL(MariaDB) 使用的是包管理器提供的预编译版，Apache 和 PHP 是我自己从源码编译的，那是哪里的问题？数据库本身直接从终端登录，操作全部正常，难道是连接过程中的问题？为了排错，我写了个很常见的数据库连接语句，这个是传统方式连接 MySQL 数据库常用的方法。
```
$connect = mysql_connect('localhost', 'root', '');
if ($connect) {
	die('Could not connect to MySQL' . mysql_error());
}
mysql_clone($connect);
```
刷新了一下浏览器你猜提示啥？mysql_conntct(): No such file or directory……. 我瞬间就蒙13了，果然是连接过程出了问题。经过我跋山涉水、地毯式搜索后基本将问题原因定位到了 PHP 无法连接到 MySQL 上。那么，PHP 是如何连接 MySQL 的？这个问题还是不谈了，可以看这里 mysql.sock 的神奇作用。那么是 mysql.sock 的路径有问题呢还是 PHP 配置文件有问题？答案肯定是 PHP 配置文件有问题，至于前天为啥能用我也是百思不得其解，如今也无从考究了。

废话了半天跟文章标题有毛线关系？好像确实没啥关系，借着这个问题顺便记录一下 ArchLinux 上搭建 LANMP 环境的过程吧。

## 安装 MySQL

MySQL 我直接用包管理器的预编译版了，一条 pacman 命令搞定。
```
sudo pacman -S mariadb
```
不要问我为什么是 MariaDB。这样还没安装完，还得初始化数据库。
```
sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```
然后启动 MySQL 服务。
```
sudo systemctl enable mysqld
sudo systemctl start mysqld
```
登录 MySQL 账号，查看 mysql.sock 文件的路径，可以看到 `UNIX socket: /run/mysqld/mysqld.sock`。
```
mysql -u root -p
MariaDB [(none)]> STATUS;
```

## 安装 Apache
Nginx 用户跳过此节～

下载最新的源码。
```
wget -q http://mirrors.cnnic.cn/apache//httpd/httpd-2.4.23.tar.gz
tar xf httpd-2.4.23.tar.gz
cd httpd-2.4.23
./configure --prefix=/usr/local/httpd --with-mpm=prefork
nice make -j4
sudo make install
```

## 安装 Nginx
Apache 用户跳过此节～

下载最新的源码
```
wget http://nginx.org/download/nginx-1.11.5.tar.gz
tar xf nginx-1.11.5.tar.gz
cd nginx-1.11.5
./configure --prefix=/usr/local/nginx --with-http_v2_module --with-http_ssl_module --with-http_gzip_static_module --with-cc-opt=-Wno-deprecated-declarations
nice make -j4
sudo make install
```

## 安装 PHP

PHP 放到最后才安装是因为如果你使用的是 Apache，那么 PHP 编译过程中需要生成一个 libphp5.so 的库文件给 Apache 使用，生成这个文件需要 Apache 的 apxs，为了兼容性就不使用 PHP 7 了，还是使用 PHP 5.6 的版本。

2016 年 11 月 15 日发现，ArchLinux 的 readline 更新到最新的 7 了，所以运行 php-cgi 时就会报错找不到 `libreadline.so.6`，这个包也得自己编译了～
```
wget ftp://ftp.gnu.org/gnu/readline/readline-6.3.tar.gz
tar xf readline-6.3.tar.gz
cd readline-6.3
./configure --prefix=/usr/local/readline
nice make -j4
sudo make install
sudo ln -s /usr/local/readline/lib/libreadline.so.6.3 /usr/lib/libreadline.so.6
sudo ln -s /usr/local/readline/lib/libreadline.so.6.3 /usr/lib64/libreadline.so.6
```

然后是编译 PHP 了，如果你使用的是 Apache 的话
```
wget -q http://cn2.php.net/distributions/php-5.6.27.tar.gz
tar xf php-5.6.27.tar.gz
cd php-5.6.27
./configure --prefix=/usr/local/php/5.6 --with-apxs2=/usr/local/httpd/bin/apxs --with-mysql --with-mysqli --with-pdo-mysql --with-readline --enable-mbstring
nice make -j4
sudo make install
sudo cp php.ini-production /usr/local/php/lib/php.ini
```
我前天 php.ini 还是复制到 etc 目录的今天就复制到 lib 目录了，因为我从 phpinfo() 里看到配置文件的默认目录竟然是在 lib 下而不是 etc 我也是醉了。

**需要注意的是：如果你同时编译了多个相同大版本号，不同小版本号的 PHP 例如同时编译了 5.5 和 5.6，那么需要在编译完第一个版本后，手动将 `/usr/local/httpd/modules/libphp5.so` 改成其他名字，例如我第一个先编译 5.5，那么先将 libphp5.so 改成 libphp5.5.so 后再编译 5.6，因为生成的文件名都是 libphp5.so，后面编译的会把前面的覆盖掉。**

如果你是用的是 Nginx 的话，去掉 `./configure` 参数里的 `--with-apxs2=/usr/local/httpd/bin/apxs` 即可。

## 编译 PHP 模块
因为我需要使用到 Redis 和 cURL 模块，所以还得编译一下这两个模块。

cURL 模块
```
cd php-5.6.27/ext/curl
/usr/local/php/5.6/bin/phpize
./configure
nice make -j4
sudo make install
```

Redis 模块
```
cd php-5.6.27/ext
git clone https://github.com/phpredis/phpredis.git
cd phpredis
/usr/local/php/5.6/bin/phpize
./configure
nice make -j4
sudo make install
```

编译好的模块都在 `/usr/local/php/5.6/lib/php/extensions/no-debug-non-zts-20131226` 下，需要在配置文件里加载所需要的模块。

打开 php.ini，定位到 `extension_dir`，添加一下语句指定扩展模块存放的目录以及需要加载哪些模块。
```
extension_dir = /usr/local/php/5.6/lib/php/extensions/no-debug-non-zts-20131226/
extension = curl.so
extension = redis.so
```

## 整合 Apache 和 PHP
Nginx 用户跳过此节～

这里有几个要做的，一个是首先要配置自己编译的 Apache 的启动脚本，另外是要配置 Apache 支持 PHP 解析。
先创建一个服务配置文件 /usr/lib/systemd/system/httpd.service，然后然后添加以下的配置内容。
```
[Unit]
Description=Apache Web Server
After=network.target remote-fs.target nss-lookup.target
[Service]
Type=simple
ExecStart=/usr/local/httpd/bin/httpd -k start -DFOREGROUND
ExecStop=/usr/local/httpd/bin/httpd -k graceful-stop
ExecReload=/usr/local/httpd/bin/httpd -k graceful
PrivateTmp=true
LimitNOFILE=infinity
KillMode=mixed
[Install]
WantedBy=multi-user.target
```

打开 Apache 的配置文件 /usr/local/httpd/conf/httpd.conf，配置一下 PHP 相关的几个参数：

搜索一下 `LoadModule`，应该可以找到 `LoadModule php5_module modules/libphp5.so`，根据你的具体情况自行更正比如我的是 `LoadModule php5_module modules/libphp5.6.so`，如果没有的话就自己加上，接下来找个位置只要不是文件的开头和结尾，加入 PHP 格式的 Index。
```
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>
```
最后再搜索一下 `AddType，加入` PHP 的 `AddType application/x-httpd-php .php`。

启动、关闭、重启 Apache 服务、查看状态。
```
sudo systemctl start httpd
sudo systemctl stop httpd
sudo systemctl restart httpd
sudo systemctl status httpd
```

写个 `phpinfo()` 看看吧。

## 整合 Nginx 和 PHP
Apache 用户跳过此节～

Nginx 和 PHP 通信可以通过 php-fpm 或者 php-cgi，这里以 php-cgi 为例
首先以 php-cgi 方式运行 PHP，监听在 9000 端口
```
sudo /usr/local/php/5.6/bin/php-cgi -b 9000 &
```

然后备份 Nginx 配置文件 `/usr/local/nginx/conf/nginx.conf`，将配置文件内容替换为
```
user  ikke ikke;
worker_processes  4;

#Specifies the value for maximum file descriptors that can be opened by this process. 
worker_rlimit_nofile 65535;

events {
	use epoll;
	worker_connections 65535;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #charset  gb2312;

    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_max_body_size 8m;

    sendfile on;
    tcp_nopush     on;

    keepalive_timeout 60;

    tcp_nodelay on;

    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;

    gzip on;
    gzip_min_length  1k;
    gzip_buffers     4 16k;
    gzip_http_version 1.0;
    gzip_comp_level 2;
    gzip_types       text/plain application/x-javascript text/css application/xml;
    gzip_vary on;
    #limit_zone  crawler  $binary_remote_addr  10m;
    log_format '$remote_addr - $remote_user [$time_local] "$request" '
		'$status $body_bytes_sent "$http_referer" '
		'"$http_user_agent" "$http_x_forwarded_for"';
    include vhosts/*.conf;
}
```

创建 vhosts 目录，并为每个 vhost 使用独立的配置文件。
```
cd /usr/local/nginx/conf
sudo mkdir vhosts
cd vhosts
sudo touch default.conf
```

其中 default.conf 的内容大致类似
```
server {
    server_name localhost;
    listen 80;

    root /var/www/html;

    index  index.php index.html index.htm;
    
    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

写个 `phpinfo()` 看看吧。

## 整合 MySQL 和 PHP

这个才是前言里提到的问题，大概就是 PHP 无法定位到 mysql.sock 的位置。
翻到前面，记住 STATUS 查询到的 mysql.sock 的位置比如我的是 `/run/mysqld/mysqld.sock`，然后打开 php.ini，找到 `pdo_mysql.default_socket`、`mysql.default_socket` 和 `mysqli.default_socket`，把后面的默认路径补上，比如 `mysqli.default_socket = /run/mysqld/mysqld.sock`，然后重启 Apache/Nginx 服务器，问题解决。
