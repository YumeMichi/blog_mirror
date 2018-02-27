---
title: CentOS 编译安装 Nginx 环境
date: 2017-06-07 06:29:43
tags:
  - CentOS
  - Nginx
categories:
  - Note
---

## 前言
前段时间更换服务器后一直没有重新编译 Nginx，一直用的 Ubuntu 预编译的二进制包，最近换成 CentOS 后，还是得重新编译一下，无奈我早已忘了当初编译 Nginx 的时候都编译了哪些扩展，只能隐约的记得 LibreSSL。

这次又重新查了一些相关的资料，重新编译了一次顺便把过程记录一下以备不时之需。

## 编译环境
先安装一些基本的编译工具和依赖。
```
yum -y install openssl openssl-devel libxml2-devel libxslt-devel perl-devel perl-ExtUtils-Embed
```

添加一个 Nginx 运行时的用户，原则上不建议使用 root 权限运行。
```
useradd -r -s /bin/false -M nginx
```

<!--more-->
建立临时文件夹用于存放源码，下载好所需的软件包。
```
mkdir nginx_build && cd nginx_build
wget http://nginx.org/download/nginx-1.13.1.tar.gz
wget https://ftp.openbsd.org/pub/OpenBSD/LibreSSL/libressl-2.5.4.tar.gz
wget http://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.40.tar.gz

tar xvf nginx-1.13.1.tar.gz
tar xvf libressl-2.5.4.tar.gz -C nginx-1.13.1
tar xvf pcre-8.40.tar.gz -C nginx-1.13.1
```

## 编译 Nginx+LibreSSL+PCRE
### 编译 Nginx
```
cd nginx-1.13.1
mkdir -p /var/tmp/nginx/{client,proxy,fastcgi,uwsgi,scgi}
mkdir -p /var/run/nginx
./configure \
    --prefix=/etc/nginx \
    --sbin-path=/usr/sbin/nginx \
    --conf-path=/etc/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \
    --pid-path=/run/nginx.pid \
    --lock-path=/var/run/nginx.lock \
    --http-client-body-temp-path=/var/tmp/nginx/client \
    --http-proxy-temp-path=/var/tmp/nginx/proxy \
    --http-fastcgi-temp-path=/var/tmp/nginx/fastcgi \
    --http-uwsgi-temp-path=/var/tmp/nginx/uwsgi \
    --http-scgi-temp-path=/var/tmp/nginx/scgi \
    --user=nginx \
    --group=nginx \
    --with-http_ssl_module \
    --with-http_realip_module \
    --with-http_addition_module \
    --with-http_sub_module \
    --with-http_dav_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_secure_link_module \
    --with-http_stub_status_module \
    --with-http_auth_request_module \
    --with-file-aio \
    --with-http_v2_module \
    --with-ld-opt="-lrt" \
    --with-openssl=libressl-2.5.4 \
    --with-pcre=pcre-8.40 \
    --with-pcre-jit
make && make install
```

### 设置 Nginx 服务脚本
编辑 `/lib/systemd/system/nginx.service`，输入一下内容。
```
[Unit]
Description=The nginx HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

启动服务。
```
systemctl daemon-reload
systemctl start nginx.service
systemctl status nginx.service
```

## Let's Encrtpy! 证书设置
此部分开始与本文标题没有关系，因为是一起做的所以干脆也一起记录一下。

### 安装 certbot
我这里是通过 yum 安装的，需要用到 EPEL 源，不知道何为 EPEL 可以看一下 [这个](http://fedoraproject.org/wiki/EPEL)。
```
yum -y install certbot
```

### 生成域名证书
注意：该域名必须得解析到当前服务器的 IP 上哈！
```
certbot certonly --standalone --email do4suki@gmail.com -d blog.ikke.moe -d ikke.moe -d rom.ikke.moe
```
生成的证书文件在 `/etc/letsencrypt/live/blog.ikke.moe/` 下，其中 `fullchain.pem` 是公钥，`privkey.pem` 是私钥。

### 配置 Nginx
个人习惯单独建立一个 `vhosts` 文件夹用于存放各个站点的配置文件而不是去动主配置文件，这个类似于 Ubuntu 下包管理安装的 Nginx，有 `site-enabled` 这个目录。
```
cd /etc/nginx
mkdir vhosts
```

然后记得在 `nginx.conf` 里引入该目录下的配置文件。在 `http` 块下加上 `include vhosts/*.conf;` 就可以了。

### 添加网站配置文件
以我的博客为例，因为存放的都是 hexo 生成的静态文件，所以没有其他额外的东西，比较简单仅供参考。
```
server {
        listen 80 default;
        server_name www.ikke.moe ikke.moe;
        return 301 https://blog.ikke.moe$request_uri;
}
server {
        listen 443 ssl http2;
        server_name blog.ikke.moe;
        server_tokens off;
        root /var/www/ikke.moe;

        ssl_certificate /etc/letsencrypt/live/blog.ikke.moe/fullchain.pem; 
        ssl_certificate_key /etc/letsencrypt/live/blog.ikke.moe/privkey.pem;
        ssl_ciphers EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
        ssl_prefer_server_ciphers on;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
        ssl_session_cache shared:SSL:50m;
        ssl_session_timeout 1d;

}
```

重启 Nginx 服务。
```
systemctl restart nginx.service
```

最后如果有安装防火墙记得开放相应的端口。


## 参考资料
 - [CentOS 7 编译安装 Nginx 1.9.7](http://blog.csdn.net/u014595668/article/details/50188813)
 - [Nginx 替换 OpenSSL 为 LibreSSL](https://www.lidaren.com/archives/1702)
 - [Belphemur/build_nginx.sh](https://gist.github.com/Belphemur/3c022598919e6a1788fc)
