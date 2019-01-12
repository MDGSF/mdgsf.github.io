---
layout: post
title: "[Lua-love2d] 打包发布"
date: 2017-08-12
author: mdgsf
comments: true
categories: lua
tags: lua
description:
published: true #default true
---

1. 进入game目录，game目录就是游戏的代码目录。

2. 全选，右键压缩成 game.zip

3. 将 game.zip 重命名为 game.love

4. 将 game.love 复制到 love游戏引擎的安装目录下。

5. copy /b love.exe+game.love game.exe

6. 然后会生成 game.exe

7. 把game.exe和love游戏引擎目录下的dll一起打包发布就好了。







