---
hidden: true
title: Sublime Text 3 中文支持
date: 2015-10-30 21:07:51
tags:
  - Sublime Text
categories:
  - Linux
---

## 安装 Sublime Text 3
以 Ubuntu 为例，先到 [Sublime Text 3 官网](http://www.sublimetext.com/3)下载 deb 安装包
```bash
wget http://c758482.r82.cf2.rackcdn.com/sublime-text_build-3083_amd64.deb
sudo dpkg -i sublime-text_build-3083_amd64.deb
```

## 安装编译环境和依赖
```bash
sudo apt-get install build-essential libgtk2.0-dev
```
<!--more-->

## 编译 patch
将以下代码保存为 `sublime_imfix.c`，文件名路径随意了其实=。=
```c
/*
 * sublime-imfix.c
 * Use LD_PRELOAD to interpose some function to fix sublime input method support for linux.
 * By Cjacker Huang <jianzhong.huang at i-soft.com.cn> *
 *
 * gcc -shared -o libsublime-imfix.so sublime_imfix.c  `pkg-config --libs --cflags gtk+-2.0` -fPIC
 * LD_PRELOAD=./libsublime-imfix.so sublime_text
 */
#include <gtk/gtk.h>
#include <gdk/gdkx.h>

typedef GdkSegment GdkRegionBox;

struct _GdkRegion
{
    long size;
    long numRects;
    GdkRegionBox *rects;
    GdkRegionBox extents;
};

GtkIMContext *local_context;

void
gdk_region_get_clipbox (const GdkRegion *region,
                        GdkRectangle    *rectangle)
{
    g_return_if_fail (region != NULL);
    g_return_if_fail (rectangle != NULL);

    rectangle->x = region->extents.x1;
    rectangle->y = region->extents.y1;
    rectangle->width = region->extents.x2 - region->extents.x1;
    rectangle->height = region->extents.y2 - region->extents.y1;
    GdkRectangle rect;
    rect.x = rectangle->x;
    rect.y = rectangle->y;
    rect.width = 0;
    rect.height = rectangle->height;

    //The caret width is 2;
    //Maybe sometimes we will make a mistake, but for most of the time, it should be the caret.
    if (rectangle->width == 2 && GTK_IS_IM_CONTEXT(local_context)) {
        gtk_im_context_set_cursor_location(local_context, rectangle);
    }
}

//this is needed, for example, if you input something in file dialog and return back the edit area
//context will lost, so here we set it again.

static GdkFilterReturn event_filter (GdkXEvent *xevent, GdkEvent *event, gpointer im_context)
{
    XEvent *xev = (XEvent *)xevent;

    if (xev->type == KeyRelease && GTK_IS_IM_CONTEXT(im_context)) {
        GdkWindow *win = g_object_get_data(G_OBJECT(im_context), "window");

        if (GDK_IS_WINDOW(win)) {
            gtk_im_context_set_client_window(im_context, win);
        }
    }

    return GDK_FILTER_CONTINUE;
}

void gtk_im_context_set_client_window (GtkIMContext *context,
                                       GdkWindow    *window)
{
    GtkIMContextClass *klass;
    g_return_if_fail (GTK_IS_IM_CONTEXT (context));
    klass = GTK_IM_CONTEXT_GET_CLASS (context);

    if (klass->set_client_window) {
        klass->set_client_window (context, window);
    }

    if (!GDK_IS_WINDOW (window)) {
        return;
    }

    g_object_set_data(G_OBJECT(context), "window", window);
    int width = gdk_window_get_width(window);
    int height = gdk_window_get_height(window);

    if (width != 0 && height != 0) {
        gtk_im_context_focus_in(context);
        local_context = context;
    }

    gdk_window_add_filter (window, event_filter, context);
}
```

然后执行编译命令生成对应的共享对象
```bash
gcc -shared -o libsublime-imfix.so sublime_imfix.c `pkg-config --libs --cflags gtk+-2.0` -fPIC
```

笔者电脑上已经安装了搜狗输入法了，用以下方法在终端中运行 Sublime Text 测试是否能输入中文
```bash
LD_PRELOAD=./libsublime-imfix.so subl
```

## 修复快捷方式
每次运行 Sublime Text 都要在终端里输入一串参数很烦吧？明明有快捷方式不用=。=

将 `libsublime-imfix.so` 拷贝到 `/usr/lib/` 下，然后修改快捷方式文件 `/usr/share/applications/sublime_text.desktop`，修改以下两处：
1. `Exec=/opt/sublime_text/sublime_text %F` 修改为 `Exec=bash -c 'LD_PRELOAD=/usr/lib/libsublime-imfix.so /opt/sublime_text/sublime_text' %F`
2. `Exec=/opt/sublime_text/sublime_text -n` 修改为 `Exec=bash -c 'LD_PRELOAD=/usr/lib/libsublime-imfix.so /opt/sublime_text/sublime_text' -n`
事实上就是带参数运行～

顺便分享两枚可用的 Sublime Text 3 序列号
```
----- BEGIN LICENSE -----
K-20
Single User License
EA7E-940129
3A099EC1 C0B5C7C5 33EBF0CF BE82FE3B
EAC2164A 4F8EC954 4E87F1E5 7E4E85D6
C5605DE6 DAB003B4 D60CA4D0 77CB1533
3C47F579 FB3E8476 EB3AA9A7 68C43CD9
8C60B563 80FE367D 8CAD14B3 54FB7A9F
4123FFC4 D63312BA 141AF702 F6BBA254
B094B9C0 FAA4B04C 06CC9AFC FD412671
82E3AEE0 0F0FAAA7 8FA773C9 383A9E18
------ END LICENSE ------
```

```
----- BEGIN LICENSE -----
J2TeaM
2 User License
EA7E-940282
45CB0D8F 09100037 7D1056EB A1DDC1A2
39C102C5 DF8D0BF0 FC3B1A94 4F2892B4
0AEE61BA 65758D3B 2EED551F A3E3478C
C1C0E04E CA4E4541 1FC1A2C1 3F5FB6DB
CFDA1551 51B05B5D 2D3C8CFE FA8B4285
051750E3 22D1422A 7AE3A8A1 3B4188AC
346372DA 37AA8ABA 6EB30E41 781BC81F
B5CA66E3 A09DBD3A 3FE85BBD 69893DBD
------ END LICENSE ------
```

## 参考文章
  - [点滴记录——在Ubuntu 14.04中使SublimeText 3支持中文输入法](http://blog.csdn.net/cywosp/article/details/32350899)
  - [Sublime Forum - Input method support](http://www.sublimetext.com/forum/viewtopic.php?f=3&t=7006&start=10#p41343)

