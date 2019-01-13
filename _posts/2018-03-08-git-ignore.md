---
layout: post
title:  "[Git] gitignore修改不起作用"
date:   2018-03-08
comments: true
categories: Git
tags: git
description:
published: true
---

在git使用过程中有时会遇到修改了.gitignore文件，修改了之后发现，不能起作用，这是因为git存在缓存问题，所以做一下步骤即可：

```
git rm -r --cached .
git add .
git commit -m "update gitignore"
```
