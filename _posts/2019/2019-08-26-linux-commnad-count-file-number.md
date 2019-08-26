---
layout: post
title:  "[Linux][常用命令] 计算当前目录下文件个数"
date:   2019-08-26
comments: true
categories: Linux
tags: [ls, awk, wc]
description:
published: true
---

### 查看当前目录下文件个数

```
ls -lh | awk '/^-/{print}' | wc -l
```

### 查看当前目录下，包括子目录下的文件个数

```
ls -lhR | awk '/^-/{print}' | wc -l
```

### 查看当前目录下，包括子目录下的文件个数

```
find . -type f | grep -v '^\./\.' | wc -l
```
