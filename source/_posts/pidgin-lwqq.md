---
hidden: true
title: Pidgin 安装 lwqq 模块
categories:
 - Linux
date: 2014-07-03 11:13:31
tags:
  - Linux
  - Debian
  - Pidgin
  - QQ
---

一段時間的debian 7的服務器使用, 已經讓我徹底的喜歡上debian了, 鼓搗了一下把本子上的fedora也換成了debian 7...
本來是想說直接用WebQQ了, 但是這貨實在不穩定啊~ 而且網頁也麻煩, Chromium也很吃內存, 試了一下SmartQQ, 還是算了吧...這貨不想噴了...

正好見到IsoaSFlus醬在用pidgin, 想想乾脆還是用pidgin吧, 安裝個lwqq的插件就可以使用WebQQ協議了!
但是問題又來了, IsoaSFlus醬用的是PPA源, 這個源只支持ubuntu, 我試了官網所有版本都不能在debian上使用, 於是只能轉投github上看官方文檔, 源碼編譯安裝, 折騰了好一會兒, 現在把具體步驟記一下:

首先當然是先安裝pidgin了
```
aptitude install pidgin```

接下來是lwqq/pidgin-lwqq的處理, 由於現在lwqq和pidgin-lwqq已經分家了, 所以需要分別編譯:

<!--more-->

安裝基本依賴項:
```
aptitude install build-essential cmake pkg-config libcurl4-openssl-dev libsqlite3-dev libmozjs185-dev libev-dev```

下載lwqq的源碼並且編譯安裝:
```
git clone https://github.com/xiehuc/lwqq.git
cd lwqq
mkdir build
cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr #注意此參數不加上的話pidgin是無法檢測到lwqq的, 這個問題困擾了筆者好久233
make -j4
sudo make install```

接下來下載pidgin-lwqq的源碼並且編譯安裝:
```
git clone https://github.com/xiehuc/pidgin-lwqq.git
cd pidgin-lwqq
git submodule init
git submodule update
mkdir build
cd build
cmake ..
make -j4
sudo make install```

OK! 這樣一來就安裝完畢了, 打開pidgin帳號設置裏就可以看到WebQQ協議了

