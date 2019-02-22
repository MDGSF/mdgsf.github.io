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

###  编辑定时任务

```
crontab -e
```

### 查看定时任务

```
$ crontab -l
*/1 * * * * /home/huangjian/local/BashUtils/nginx/rotate.sh
```

### cron

#### 每分钟执行一次

```
*/1 * * * * test.sh
```

#### 每天00:00执行一次

```
00 00 * * * test.sh
```

