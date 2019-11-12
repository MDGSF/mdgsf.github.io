---
layout: post
title: "[TeX] ubuntu 16.04 安装"
date: 2019-11-12
author: mdgsf
comments: true
categories: TeX
tags: [TeX]
description:
published: true #default true
---

下载地址：

[https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/Images/](https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/Images/)

```
sudo mount texlive.iso /mnt
sudo perl /mnt/install-tl
```

```
======================> TeX Live installation procedure <=====================

======>   Letters/digits in <angle brackets> indicate   <=======
======>   menu items for actions or customizations      <=======

 Detected platform: GNU/Linux on x86_64

 <B> set binary platforms: 1 out of 5

 <S> set installation scheme: scheme-full

 <C> set installation collections:
     40 collections out of 41, disk space required: 5845 MB

 <D> set directories:
   TEXDIR (the main TeX directory):
     /usr/local/texlive/2019
   TEXMFLOCAL (directory for site-wide local files):
     /usr/local/texlive/texmf-local
   TEXMFSYSVAR (directory for variable and automatically generated data):
     /usr/local/texlive/2019/texmf-var
   TEXMFSYSCONFIG (directory for local config):
     /usr/local/texlive/2019/texmf-config
   TEXMFVAR (personal directory for variable and automatically generated data):
     ~/.texlive2019/texmf-var
   TEXMFCONFIG (personal directory for local config):
     ~/.texlive2019/texmf-config
   TEXMFHOME (directory for user-specific files):
     ~/texmf

 <O> options:
   [ ] use letter size instead of A4 by default
   [X] allow execution of restricted list of programs via \write18
   [X] create all format files
   [X] install macro/font doc tree
   [X] install macro/font source tree
   [ ] create symlinks to standard directories
   [X] after install, set CTAN as source for package updates

 <V> set up for portable installation

Actions:
 <I> start installation to hard disk
 <P> save installation profile to 'texlive.profile' and exit
 <H> help
 <Q> quit

Enter command:
```

```
# add this to .zshrc or .profile
export MANPATH=$MANPATH:/usr/local/texlive/2019/texmf-dist/doc/man
export INFOPATH=$INFOPATH:/usr/local/texlive/2019/texmf-dist/doc/info
export PATH=$PATH:/usr/local/texlive/2019/bin/x86_64-linux
```

```
sudo apt-get install texlive-lang-cjk
```
