---
layout: post
title:  "[C/C++] nng 编译"
date:   2020-10-13
comments: true
categories: C/C++
tags: [C,nanomsg,nng]
description:
published: true
---

项目地址：https://github.com/nanomsg/nng

先安装 ninja 依赖。

```bash
sudo apt install ninja
sudo apt install ninja-build
```

编译静态库：

```bash
mkdir build
cd build
cmake -G Ninja ..
ninja
```

编译动态库：

```bash
mkdir build
cd build
cmake -DBUILD_SHARED_LIBS=ON -G Ninja ..
ninja
```

