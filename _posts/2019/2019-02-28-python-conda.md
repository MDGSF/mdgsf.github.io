---
layout: post
title:  "[python] conda"
date:   2019-02-28
comments: true
categories: python
tags: [Python,conda]
description:
published: true
---

### 更新

```
conda update conda
conda update anaconda
```

### 列出所有环境

```
conda env list
```

### 移除环境

```
conda env remove -n env_name
```

### 创建一个新的环境 hello

```
conda create -n hello python=3
```

### 进入环境 hello

```
source activate hello
```

### 退出当前环境

```
source deactivate
```

### 克隆一个环境

通过创建环境的克隆来创建环境的精确副本。这里我们将克隆 snowflakes 创建一个名为 flowers 的精确副本：

```
conda create --name flowers --clone snowflakes
```

### 安装软件

```
conda install flask
```

### 参考链接

[https://www.jianshu.com/p/17288627b994](https://www.jianshu.com/p/17288627b994)
