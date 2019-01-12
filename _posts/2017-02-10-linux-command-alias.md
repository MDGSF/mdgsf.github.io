---
layout: post
title:  "[Linux常用命令] alias"
date:   2017-02-10
comments: true
categories: linux
tags: ls
description:
published: true
---


### 1 不带参数，显示出当前的所有重命名

<img src="{{ site.url }}/images/2017/02/10_12.png" alt="2017_02_10_12" />


### 2 将cd /mnt/share 重命名为zm

<img src="{{ site.url }}/images/2017/02/10_13.png" alt="2017_02_10_13" />


### 3 取消重命名

<img src="{{ site.url }}/images/2017/02/10_14.png" alt="2017_02_10_14" />


### 4 重命名永久生效

```
vim ~/.bashrc
alias zm='cd /mnt/share'
:wq
source .bashrc
```
