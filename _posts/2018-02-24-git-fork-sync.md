---
layout: post
title:  "[git] fork同步"
date:   2018-02-24
comments: true
categories: git
tags: git
description:
published: true
---

## 步骤

**1. 给fork配置一个remote**

```
Administrator@ROEGRBXEPPWF49V MINGW64 /e/git
$ cd GCTT

Administrator@ROEGRBXEPPWF49V MINGW64 /e/git/GCTT (master)
$ git remote -v
origin  https://github.com/MDGSF/GCTT.git (fetch)
origin  https://github.com/MDGSF/GCTT.git (push)

Administrator@ROEGRBXEPPWF49V MINGW64 /e/git/GCTT (master)
$ git branch
* master
```

```
Administrator@ROEGRBXEPPWF49V MINGW64 /e/git/GCTT (master)
$ git remote add upstream https://github.com/studygolang/GCTT

Administrator@ROEGRBXEPPWF49V MINGW64 /e/git/GCTT (master)
$ git remote -v
origin  https://github.com/MDGSF/GCTT.git (fetch)
origin  https://github.com/MDGSF/GCTT.git (push)
upstream        https://github.com/studygolang/GCTT (fetch)
upstream        https://github.com/studygolang/GCTT (push)

Administrator@ROEGRBXEPPWF49V MINGW64 /e/git/GCTT (master)
$ git branch
* master
```

**2. 从上游仓库 fetch 分支和提交点，传送到本地，并会被存储在一个本地分支 upstream/master**

```
Administrator@ROEGRBXEPPWF49V MINGW64 /e/git/GCTT (master)
$ git fetch upstream
remote: Counting objects: 74, done.
remote: Compressing objects: 100% (32/32), done.
remote: Total 74 (delta 50), reused 65 (delta 42), pack-reused 0
Unpacking objects: 100% (74/74), done.
From https://github.com/studygolang/GCTT
 * [new branch]      master     -> upstream/master

Administrator@ROEGRBXEPPWF49V MINGW64 /e/git/GCTT (master)
$ git branch
* master
```

**3. 把 upstream/master 分支合并到本地 master 上**

```
Administrator@ROEGRBXEPPWF49V MINGW64 /e/git/GCTT (master)
$ git merge upstream/master
Auto-merging published/tech/为 Go Web-apps 编写 Dockerfiles 的终极指南.md
Auto-merging published/tech/Go语言的错误处理.md
Merge made by the 'recursive' strategy.
 ...224\231\350\257\257\345\244\204\347\220\206.md" | 118 ++---
 ...273\210\346\236\201\346\214\207\345\215\227.md" |  53 ++-
 .../tech/Language Mechanics On Memory Profiling.md | 497 +++++++++++++++++++++
 .../Language Mechanics On Stacks And Pointers.md   | 290 ++++++++++++
 .../Using Go as a scripting language in Linux.md   | 145 ++++++
 5 files changed, 1019 insertions(+), 84 deletions(-)
 rename "translated/tech/Go\350\257\255\350\250\200\347\232\204\351\224\231\350\257\257\345\244\204\347\220\206.md" => "published/tech/Go\350\257\255\350\250\200\347\232\204\351\224\231\350\257\257\345\244\204\347\220\206.md" (80%)
 rename "translated/tech/\344\270\272 Go Web-apps \347\274\226\345\206\231 Dockerfiles \347\232\204\347\273\210\346\236\201\346\214\207\345\215\227.md" => "published/tech/\344\270\272 Go Web-apps \347\274\226\345\206\231 Dockerfiles \347\232\204\347\273\210\346\236\201\346\214\207\345\215\227.md" (95%)
 create mode 100644 translated/tech/Language Mechanics On Memory Profiling.md
 create mode 100644 translated/tech/Language Mechanics On Stacks And Pointers.md
 create mode 100644 translated/tech/Using Go as a scripting language in Linux.md
```

**4. 更新到GitHub的fork上去**

```
Administrator@ROEGRBXEPPWF49V MINGW64 /e/git/GCTT (master)
$ git push origin master
fatal: HttpRequestException encountered.
   An error occurred while sending the request.
Username for 'https://github.com': 1342042894@qq.com
Password for 'https://1342042894@qq.com@github.com':
Counting objects: 78, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (78/78), done.
Writing objects: 100% (78/78), 42.00 KiB | 3.23 MiB/s, done.
Total 78 (delta 53), reused 0 (delta 0)
remote: Resolving deltas: 100% (53/53), completed with 5 local objects.
To https://github.com/MDGSF/GCTT.git
   4f2b86d..3624816  master -> master
```

## 参考链接

[https://gaohaoyang.github.io/2015/04/12/Syncing-a-fork/](https://gaohaoyang.github.io/2015/04/12/Syncing-a-fork/)

[https://help.github.com/articles/configuring-a-remote-for-a-fork/](https://help.github.com/articles/configuring-a-remote-for-a-fork/)

[https://help.github.com/articles/syncing-a-fork/](https://help.github.com/articles/syncing-a-fork/)
