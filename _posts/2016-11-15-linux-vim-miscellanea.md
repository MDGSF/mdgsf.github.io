---
layout: post
title:  "[Linux][vim] delete \'\\r\' in window file"
date:   2016-11-15
comments: true
categories: Linux
tags: [Linux,Vim]
description:
published: true
---

删除文件末尾的 `\r`

```
:%s/\r//g
```
