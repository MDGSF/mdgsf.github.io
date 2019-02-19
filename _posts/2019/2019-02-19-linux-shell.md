---
layout: post
title: "[Linux] Shell"
date: 2019-02-19
author: mdgsf
comments: true
categories: Linux
tags: [Linux,Shell]
description: ""
published: true #default true
---

### 列出所有可用的 Shell

```
$ cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/bin/dash
/bin/bash
/bin/rbash
/bin/zsh
/usr/bin/zsh
```

### 查看当前使用的 Shell

```
$ ps $$
  PID TTY      STAT   TIME COMMAND
 7356 pts/21   Ss     0:01 -zsh
```

```
$ echo $SHELL
/usr/bin/zsh
```

### 切换 Shell

```
chsh -s $(which zsh)
reboot
```

临时切换到 bash。

```
# huangjian @ huangjian-ThinkPad-T470p in ~ [17:20:31]
$ bash
huangjian@huangjian-ThinkPad-T470p:~$ exit
exit

# huangjian @ huangjian-ThinkPad-T470p in ~ [17:20:36]
$
````


