---
layout: post
title: "clang format"
date: 2019-09-16
author: mdgsf
comments: true
categories: C语言
tags: [clang]
description: ""
published: true #default true
---

安装 clang-format

```
sudo apt install clang-format
```

获取一个配置文件

```
clang-format -style=llvm -dump-config > .clang-format
clang-format -style=google -dump-config > .clang-format
```

clang-format 会自动查找 .clang-format 配置文件，当前目录找不到会找父目录。
所以可以在 HOME 目录下生成一个 .clang-format。

http://releases.llvm.org

http://clang.llvm.org/docs/ClangFormat.html#vim-integration

https://github.com/rhysd/vim-clang-format.git

https://clang.llvm.org/docs/ClangFormatStyleOptions.html

