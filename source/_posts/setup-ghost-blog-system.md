---
hidden: true
title: 搭建 Ghost  博客系统
date: 2014-10-25 13:37:09
tags:
  - Ghost
  - Node.js
  - MySQL
  - Hexo
categories:
  - 教程
---

## 前言

2014 年 12 月 13 日重建博客文章顺便重新编辑了一下，更新了 Ghost 和 MariaDB 的版本以及最后的运行部分。

本文部分参考 [SenLief](https://senlief.com/) 的 [Centos7 下安装 Ghost 博客系统](https://senlief.com/centos7xia-an-zhuang-ghostbo-ke-xi-tong/)，其余内容均为原创。

## 安装 Nginx

Debian 软件源自带 Nginx 了，直接用 aptitude 指令安装即可，Debian Jessie 最新的 Nginx 是 1.6.2 稳定版。

```
aptitude install -y zlib1g-dev libssl-dev libpcre3-dev nginx
```

## 安装 MariaDB

如果你使用的是 Debian Jessie，那么仅需要通过 aptitude 便可以安装好 MariaDB

```
aptitude install -y mariadb-server mariadb-client
```
<!--more-->

如果是较低版本的 Debian 就需要手动添加 MariaDB 的软件源，以 Debian Wheezy 为例

```
apt-get install python-software-properties
apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 0xcbcb082a1bb943db
add-apt-repository 'deb http://mirrors.neusoft.edu.cn/mariadb/repo/10.0/debian wheezy main'
apt-get update
apt-get install mariadb-server mariadb-client
```

中途会让你设定 MariaDB 的 root 密码，务必记住！注：非系统 root 密码

其他系统可以参考 MariaDB 官网的安装教程 [Setting up MariaDB Repositories](https://downloads.mariadb.org/mariadb/repositories/)

## 安装 Node.js

我一向推荐使用 NVM 来安装和管理 Node，因为我平时使用的一般都是最新版本，但是 Ghost 目前只支持 0.10 版本，这样一来使用 NVM 来同时管理多个版本的 Node 就非常的方便了。

```
curl https://raw.githubusercontent.com/creationix/nvm/v0.17.3/install.sh | bash
```

退出 Shell 重新登陆一下使得环境变量生效

查看远端仓库的 Node.js 版本

```
nvm ls-remote
```

选择一个 Node.js 版本，Ghost 只支持 v0.10 所以就装 v0.10.33

```
nvm install 0.10.33
```

设置为默认的 Node.js

```
nvm alias default 0.10.33
```

NVM 可以方便的管理多个 Node 版本，并且随时可以使用 nvm use xx_ver 互相切换。更多的用法轻敲 nvm --help

## 安装 Ghost

Ghost 是一个相对 WordPress 而言要轻量级一点的博客系统，但是安装起来不如 WordPress 简单就是了。

```
wget https://github.com/TryGhost/Ghost/releases/download/0.5.6/Ghost-0.5.6.zip
mkdir Ghost
unzip -d Ghost Ghost-0.5.6.zip
cd Ghost/
npm install --production
```

最新版的 Ghost 可以从[这里](https://github.com/TryGhost/Ghost/releases)获取到。

## 设定

首先是 MariaDB。设定字符编码，防止中文乱码。打开 /etc/mysql/my.cnf，在 [mysqld] 字段下添加以下三行：

```
collation-server     = utf8_unicode_ci  
init-connect         = 'SET NAMES utf8'  
character-set-server = utf8
```

重启 MariaDB

```
service mysql restart
```

进入 MariaDB 查看更该是否已经生效

```
mysql -u root -p
```

```
SHOW VARIABLES LIKE "%char%";
SHOW VARIABLES LIKE "%collation%";
```

如下即可：

```
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
```

```
+----------------------+-----------------+
| Variable_name        | Value           |
+----------------------+-----------------+
| collation_connection | utf8_general_ci |
| collation_database   | utf8_unicode_ci |
| collation_server     | utf8_unicode_ci |
+----------------------+-----------------+
```

为 Ghost 创建一个数据库用于保存数据

```
CREATE DATABASE ghost;
```

然后回到 Ghost 目录，更改 Ghost 的配置文件，直接从给定的模版修改即可

```
cp config.example.js config.js
```

打开配置文件 config.js，可以看到一行提示 When running Ghost in the wild, use the production environment，也就是它会使用 production 定义的数据库配置，所以我们只需要把默认的 production 改成其他名字例如 sqlite3，然后找到 testing-mysql，将其改称 production 即可，最后填写一下 MariaDB 的配置。

```
production: {
    url: 'http://ghost.dsy.im',
    database: {
        client: 'mysql',
        connection: {
            host     : '127.0.0.1',
            user     : 'root',
            password : 'Your_MySQL_Password',
            database : 'ghost',
            charset  : 'utf8'
        }
    },
    server: {
        host: '127.0.0.1',
        port: '2369'
    },
    logging: false
},
```

**注：绑定的域名的 DNS 记录中记得添加一条 A 记录指向该主机 IP**

最后是 Nginx 的设定了。切换到 Nginx 的配置文件放置目录，新建一个配置文件

```
cd /etc/nginx/sites-available/
touch ghost.conf
ln -s /etc/nginx/sites-available/ghost.conf ../sites-enabled/ghost
vim ghost.conf
```

内容为

```
server {
    listen 80;
    server_name  ghost.dsy.im;
    location / {
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   Host      $http_host;
        proxy_pass         http://127.0.0.1:2369;
    }
}
```

然后重新加载一下 Nginx 配置文件

```
nginx -s reload
```

最后再回到 Ghost 目录下，执行

```
npm start --production
```

出现如下提示就表示成功了，可以去浏览器里看看效果了！

```
> ghost@0.5.6 start /root/Ghost
> node index

Migrations: Up to date at version 003
Ghost is running... 
Your blog is now available on http://ghost.dsy.im 
Ctrl+C to shut down
```

**安装完后记得通过 域名/ghost 进入后台设置博客标题以及密码等信息**，设置完进后台就可以对博客进行设置以及写文章了。

## 附加设置

附加部分主要是主题的更换以及 https 的配置，今晚也在群里跟[大哥哥](http://ibrother.me/)以及 [SenLief](https://senlief.com/) 探讨了许久。

### 主题安装

推荐一款国人制作的主题 —— [Vno](https://github.com/onevcat/vno)

安装方法

```
cd Ghost
git clone https://github.com/onevcat/vno.git content/themes/Vno
```

然后进后台找到 Settings —— Theme，下拉列表就有 Vno 了，选择完保存即可！

其他设置神马的就自己翻后台了，下来是 https 的设置，没有购买证书的可以跳过！

### https 设置

某些时候我们会因为安(zhuang)全(bi)问题而需要使用 https，这个时候就需要另外进行一些配置了。

首先你得有机构颁发的证书，一般是一张私钥＋一张网站的证书和一张根证书，将网站的证书与根证书合并为一个文件，保存为例如 /etc/ssl/certs/blog.crt，将私钥保存为例如 /etc/ssl/certs/blog.pem，注意保存的路径，Nginx 要有读取的权限，然后打开 Ghost 的配置文件 config.js，将域名更改为 https，其他的保持不变

```
production: {
    url: 'https://ghost.dsy.im',
    database: {
        client: 'mysql',
        connection: {
            host     : '127.0.0.1',
            user     : 'root',
            password : 'Your_MySQL_Password',
            database : 'ghost',
            charset  : 'utf8'
        }
    },
    server: {
        host: '127.0.0.1',
        port: '2369'
    },
    logging: false
},
```

编辑配置 Nginx 对应的配置文件 ghost.conf

```
server {
        listen 80;
        listen 443 ssl spdy;
        server_name ghost.dsy.im;
        ssl on;
        ssl_certificate_key /etc/ssl/certs/dsy.im.pem;
        ssl_certificate /etc/ssl/certs/dsy.im.crt;
        ssl_session_timeout 5m;
        ssl_ciphers AES128+EECDH:AES128+EDH:!aNULL;
        ssl_prefer_server_ciphers on;
        ssl_protocols  TLSv1 TLSv1.1 TLSv1.2;
        location / {
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   Host $http_host;
        proxy_set_header   X-Forwarded-Proto $scheme;
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   Host      $http_host;
        proxy_pass         http://127.0.0.1:2369;
    }
}
```

重新加载一下配置文件然后启动 npm 即可。

```
nginx -s reload
npm start --production
```

## 保持后台运行

这个方法就比较多了，大家可以参考音符的这篇文章：http://blog.freedom.moe/?p=578

**注：最后这一部分是 2014 年 12 月 13 日重建博客时修改过的。**

## 样站

