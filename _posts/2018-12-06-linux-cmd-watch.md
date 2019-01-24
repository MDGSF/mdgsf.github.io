---
layout: post
title:  "[Linux] watch 命令"
date:   2018-12-06
comments: true
categories: Linux
tags: [Linux,Watch]
description:
published: true
---

```
watch nvidia-smi 
```

每隔两秒执行一次 nvidia-smi 命令

```
watch -d nvidia-smi 
```

每隔两秒执行一次 nvidia-smi 命令，高亮与上一次结果不同的地方

```
watch -d -n 1 nvidia-smi 
```

每隔一秒执行一次 nvidia-smi 命令，高亮与上一次结果不同的地方

