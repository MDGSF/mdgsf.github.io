---
layout: post
title:  "[Git][翻译] Configuring a remote for a fork"
date:   2018-02-25
comments: true
categories: Git
tags: [Git]
description:
published: true
---

# 为 fork 配置一个远程存储库

你必须配置指向 Git 中上游的远程存储库，以便把[你在 fork 中进行的更改同步](https://help.github.com/articles/syncing-a-fork)到原始存储库。这也允许你把原始存储库中所做的更改同步到 fork。

## 1. 打开 Git Bash

## 2. 列出 fork 中当前配置的远程存储库。

```
$ git remote -v
origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
```

## 3. 指定一个将和 fork 同步的新的远程上游存储库。

```
git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
```

## 4. 验证你为 fork 指定的新的上游存储库。

```
$ git remote -v
origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (fetch)
upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (push)
```

----------------

via: https://help.github.com/articles/configuring-a-remote-for-a-fork/
