---
layout: post
title:  "[Linux][常用命令] nl"
date:   2017-03-16
comments: true
categories: Linux
tags: [Ls]
description:
published: true
---

### -b  ：指定行号指定的方式，主要有两种：

-b a ：表示不论是否为空行，也同样列出行号(类似 cat -n)；

-b t ：如果有空行，空的那一行不要列出行号(默认值)；

### -n  ：列出行号表示的方法，主要有三种：

-n ln ：行号在萤幕的最左方显示；

-n rn ：行号在自己栏位的最右方显示，且不加 0 ；

-n rz ：行号在自己栏位的最右方显示，且加 0 ；

-w  ：行号栏位的占用的位数。

```
root@jian-VirtualBox:# nl aa
     1 fdsaf

     2 f
```

```
root@jian-VirtualBox:# nl -b a aa
     1 fdsaf
     2
     3 f
```

```
root@jian-VirtualBox:# nl -n ln aa
1     fdsaf

2     f
```

```
root@jian-VirtualBox:# nl -n rn aa
     1 fdsaf

     2 f
```

```
root@jian-VirtualBox:# nl -n rz aa
000001 fdsaf

000002 f
```

```
root@jian-VirtualBox:# nl -w 3 -n rz aa
001 fdsaf

002 f
```
