---
hidden: true
title: Linux 下半编译安装 VMware 
categories:
 - Linux
date: 2014-07-03 11:14:33
tags:
  - Linux
  - Debian
  - VMware
---

本子換成debian 7了, 詳見前面的日誌...

VMware安裝過程的問題, 其實也只是因爲內核源碼較新的問題, VMware需要重新編譯... 

首先先安裝VMware了~ 這裏我們只下載二進制包而不下載源碼包, 因爲我們並不需要從頭編譯... 
```
wget https://download3.vmware.com/software/wkst/file/VMware-Workstation-Full-10.0.3-1895310.x86_64.bundle
chmod u+x VMware-Workstation-Full-10.0.3-1895310.x86_64.bundle
./VMware-Workstation-Full-10.0.3-1895310.x86_64.bundle```

<!--more-->

內核問題有兩種解決方法, 一種是給VMware的vmnet打內核patch, 但是目前github上的內核patch只到3.14, 而debian 7的內核是3.20, 所以這種方法不行了, 以下是patch的項目地址:
```
https://github.com/cseader/Tools/tree/master/kernel```

使用方法:
```
cd /usr/lib/vmware/modules/source/
wget https://github.com/cseader/Tools/blob/master/kernel/kernel-3.14-vmware-filter.c.diff
cp vmnet.tar vmnet.tar.bak
tar xvf vmnet.tar vmnet-only/filter.c
patch vmnet-only/filter.c < kernel-3.14-vmware-filter.c.diff
tar -uvf vmnet.tar vmnet-only/filter.c
rm -rf vmnet-only/
vmware-modconfig --console --install-all```

另一種方法, 下載內核源碼丟到/usr/src下, 然後重新編譯VMware, 無需打patch (筆者猜測原理其實差不多~):
```
uname -a #記住內核版本, 例如筆者的是3.2.0-4-amd64
aptitude install linux-headers-3.2.0-4-amd64 #具體內核版本請參考上述命令打印出的
vmware-modconfig --console --install-all```

OK! 再運行VMware就完全正常了~~

