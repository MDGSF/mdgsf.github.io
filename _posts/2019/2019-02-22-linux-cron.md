---
layout: post
title: "[Linux] cron 定时任务"
date: 2019-02-22
author: mdgsf
comments: true
categories: Linux
tags: [Linux]
description: ""
published: true #default true
---

### 选择编辑定时任务时使用的编辑器

```
$ select-editor

Select an editor.  To change later, run 'select-editor'.
  1. /bin/ed
  2. /bin/nano        <---- easiest
  3. /usr/bin/code
  4. /usr/bin/vim.basic
  5. /usr/bin/vim.tiny

Choose 1-5 [2]: 4
```

### 编辑定时任务

编辑当前用户的定时任务

```
crontab -e
```

如果当前用户为 huangjian，`crontab -u huangjian -e` 和 `crontab -e` 是一样的。

`sudo crontab -u root -e` 编辑 root 用户的定时任务

编辑好之后，保存了，就生效了。如果要删除某个定时任务，也用 `crontab -e` 这个
命令打开 crontab 文件，删除掉了，再保存了就好了。

### 查看定时任务

```
$ crontab -l
*/1 * * * * /home/huangjian/local/BashUtils/nginx/rotate.sh
```

### 删除掉所有的定时任务

这个命令慎用！！！

```
crontab -r                删除掉当前用户的所有定时任务
sudo crontab -u root -r   删除掉 root 用户的所有定时任务
```

### 例子

#### 每分钟执行一次 test.sh

```
*/1 * * * * test.sh
```

### 每分钟输出一个当前时间到 /home/huangjian/foo.txt 文件末尾

```
*/1 * * * * echo `date` >> /home/huangjian/foo.txt
```

#### 每天00:00执行一次

```
0 0 * * * test.sh
```

