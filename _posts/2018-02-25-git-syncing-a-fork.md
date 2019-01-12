---
layout: post
title:  "[git - 翻译] Syncing a fork"
date:   2018-02-25
comments: true
categories: git
tags: git
description:
published: true
---

# 同步 fork

同步存储库的某个 fork 以使其保持与上游存储库的最新状态。

在将你的 fork 与上游存储库同步之前，必须[先配置一个指向 Git 中上游存储库的远程存储库](https://help.github.com/articles/configuring-a-remote-for-a-fork)。

### 1. 打开 Git Bash。

### 2. 将当前工作目录切换到本地项目。

### 3. 从上游存储库中获取分支和它们各自的 commits。提交到 `master` 的 commits 将会被存储在本地分支中，`upstream/master`。

```
$ git fetch upstream
remote: Counting objects: 75, done.
remote: Compressing objects: 100% (53/53), done.
remote: Total 62 (delta 27), reused 44 (delta 9)
Unpacking objects: 100% (62/62), done.
From https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY
 * [new branch]      master     -> upstream/master
```

### 4. 把你的 fork 切换到 `master` 主分支。

```
$ git checkout master
Switched to branch 'master'
```

### 5. 将来自 `upstream/master` 的更改合并到你的本地 `master` 主分支中。这会使你的 fork 的 `master` 主分支与上游存储库保持同步，而不会丢失本地的修改。

```
$ git merge upstream/master
Updating a422352..5fdff0f
Fast-forward
 README                    |    9 -------
 README.md                 |    7 ++++++
 2 files changed, 7 insertions(+), 9 deletions(-)
 delete mode 100644 README
 create mode 100644 README.md
```

如果你的本地分支没有任何的 commits 提交，Git 将会执行 “fast-forward” 合并：

```
$ git merge upstream/master
Updating 34e91da..16c56ad
Fast-forward
 README.md                 |    5 +++--
 1 file changed, 3 insertions(+), 2 deletions(-)
```

**提示：**同步你的 fork 只会更新你的本地版本库。要在 GitHub 上更新你的 fork，你必须[推送你的更改](https://help.github.com/articles/pushing-to-a-remote)。

----------------

via: https://help.github.com/articles/syncing-a-fork/
