---
layout: post
title: "[Linux] inode "
date: 2019-02-22
author: mdgsf
comments: true
categories: Linux
tags: [Linux]
description: ""
published: true #default true
---

创建一个 foo.txt 文件

```
$ touch foo.txt
$ echo "hello" > foo.txt
$ cat foo.txt
hello
```

foo.txt 文件的 inode 是 13

```
$ ls -lhi foo.txt
13 -rw-rw-r-- 1 huangjian huangjian 6 Feb 22 13:56 foo.txt

$ stat foo.txt
  File: 'foo.txt'
  Size: 6               Blocks: 8          IO Block: 4096   regular file
Device: 800h/2048d      Inode: 13          Links: 1
Access: (0664/-rw-rw-r--)  Uid: ( 1000/huangjian)   Gid: ( 1000/huangjian)
Access: 2019-02-22 13:56:35.097721182 +0800
Modify: 2019-02-22 13:56:31.645684665 +0800
Change: 2019-02-22 13:56:31.645684665 +0800
 Birth: -
```

cp 拷贝出来的文件，inode 的值改变了。

```
$ cp foo.txt foo.txt.cp
$ stat foo.txt.cp
  File: 'foo.txt.cp'
  Size: 6               Blocks: 8          IO Block: 4096   regular file
Device: 800h/2048d      Inode: 15          Links: 1
Access: (0664/-rw-rw-r--)  Uid: ( 1000/huangjian)   Gid: ( 1000/huangjian)
Access: 2019-02-22 13:58:18.050807588 +0800
Modify: 2019-02-22 13:58:18.050807588 +0800
Change: 2019-02-22 13:58:18.050807588 +0800
 Birth: -
```

给 foo.txt 创建一个硬链接，inode 不变

```
$ ln foo.txt foo.txt.hardlink
$ stat foo.txt.hardlink
  File: 'foo.txt.hardlink'
  Size: 6               Blocks: 8          IO Block: 4096   regular file
Device: 800h/2048d      Inode: 13          Links: 2
Access: (0664/-rw-rw-r--)  Uid: ( 1000/huangjian)   Gid: ( 1000/huangjian)
Access: 2019-02-22 13:56:35.097721182 +0800
Modify: 2019-02-22 13:56:31.645684665 +0800
Change: 2019-02-22 13:58:38.651024368 +0800
 Birth: -
```

给 foo.txt 创建一个软链接，inode 改变了

```
$ ln -s foo.txt foo.txt.softlink
$ stat foo.txt.softlink
  File: 'foo.txt.softlink' -> 'foo.txt'
  Size: 7               Blocks: 0          IO Block: 4096   symbolic link
Device: 800h/2048d      Inode: 16          Links: 1
Access: (0777/lrwxrwxrwx)  Uid: ( 1000/huangjian)   Gid: ( 1000/huangjian)
Access: 2019-02-22 13:59:06.099312909 +0800
Modify: 2019-02-22 13:59:00.707256254 +0800
Change: 2019-02-22 13:59:00.707256254 +0800
 Birth: -
```

mv 移动文件的时候，inode 不变。但是如果系统中有多个硬盘，mv 的时候是从一个硬盘移动到另一个硬盘的话，inode 会改变。

```
$ ls -lhi foo.txt*
13 -rw-rw-r-- 2 huangjian huangjian 6 Feb 22 13:56 foo.txt
15 -rw-rw-r-- 1 huangjian huangjian 6 Feb 22 13:58 foo.txt.cp
13 -rw-rw-r-- 2 huangjian huangjian 6 Feb 22 13:56 foo.txt.hardlink
16 lrwxrwxrwx 1 huangjian huangjian 7 Feb 22 13:59 foo.txt.softlink -> foo.txt
```

```
$ mv foo.txt foo.txt.bar
$ ls -lhi foo.txt*
13 -rw-rw-r-- 2 huangjian huangjian 6 Feb 22 13:56 foo.txt.bar
15 -rw-rw-r-- 1 huangjian huangjian 6 Feb 22 13:58 foo.txt.cp
13 -rw-rw-r-- 2 huangjian huangjian 6 Feb 22 13:56 foo.txt.hardlink
16 lrwxrwxrwx 1 huangjian huangjian 7 Feb 22 13:59 foo.txt.softlink -> foo.txt
```

修改 foo.txt.bar 的内容，硬链接也会改变，因为他们的 inode 是相同的。

```
$ echo "world" > foo.txt.bar
$ cat foo.txt.hardlink
world
```
