---
layout: post
title:  "[Qt] 从主界面返回登录界面"
date:   2016-09-25
comments: true
categories: Qt
tags: [C++,Qt]
description: 
published: true
---

### 方法一：

直接重启程序。简单，好用。

## 方法二：

隐藏主界面，显示登录界面。登录成功就隐藏登录界面，显示主界面。

这种方法会有很多的中间变量状态要清空，非常麻烦，几乎无法使用，因此不建议使用。
