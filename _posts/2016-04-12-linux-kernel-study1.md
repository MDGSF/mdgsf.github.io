---
layout: post
title:  "[Linux] 内核学习"
date:   2016-04-12
comments: true
categories: Linux
tags: linux, kernel, c, asm
description:
published: true
---

### 书籍推荐

[Linux内核学习四库全书](http://blog.csdn.net/21aspnet/article/details/6585602)

[http://www.oldlinux.org/](http://www.oldlinux.org/)



### 内核源码下载

[linux-0.11-060618-gcc4.tar.gz](http://oldlinux.org/Linux.old/kernel/0.1x/)

我们选择使用 linux-0.11-060618-gcc4.tar.gz 这个版本。


### 编译内核

[Linux 环境下编译 0.11版本内核 kernel](http://blog.chinaunix.net/uid-14735472-id-3330285.html)

tar -zxvf linux-0.11-060618-gcc4.tar.gz

修改 Makefile 里 -mcpu=i386 为 -march=i386

修改 kernel/blk_drv/blk.h 文件第87行 将 #elif 修改为 #else

将**所有** Makefile 中的CFLAGS后面加上了-fno-stack-protector

**记住：make之前要记得执行 make clean**


make

make: as86: Command not finded

出错原因：as86 汇编器未安装

解决办法：

下载dev86-0.16.3-8.i386.rpm安装 下载地址 [as86](http://www.oldlinux.org/Linux.old/study/tools/)

Ubuntu的软件包格式为deb,而RPM格式的包则是Red Hat。解决方法：

首先，我们要安装alien这一软件：

    $sudo apt-get install alien ##alien默认没有安装，所以首先要安装它

    $sudo alien xxxx.rpm  ##将rpm转换为deb,完成后会生成一个xxxx.deb

    $sudo dpkg -i xxxx.deb ##这样xxxx软件就可以安装完成了

注意，用alien转换deb包并不能保证完全顺利安装，所以如果能找到deb包，还是用deb包为好。


安装完 as86 汇编器之后，重新执行 make
