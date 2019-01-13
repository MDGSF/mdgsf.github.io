---
layout: post
title:  "[vim] c++ syntax"
date:   2016-03-05
comments: true
categories: Linux
tags: [vim,c++,syntax]
description: "vim syntax for c++"
published: true
---


### c++ syntax 文件下载

<a href="http://pan.baidu.com/s/1qWQO7q0" target="_blank">百度云</a>

把下载的文件解压之后会生成两个文件 cpp11.vim 和 cpp11_cbase.vim。

然后把这两个文件复制到  ~/.vim/syntax 目录下面，如果没有这个目录的话就自己手动建立一个。

你也可以自己修改语法的颜色定义， 在cpp11_cbase.vim 中就可以修改了。

最后还要在  .vimrc 中加一句话:

au BufNewFile,BufRead *.cpp,*.c set syntax=cpp11
