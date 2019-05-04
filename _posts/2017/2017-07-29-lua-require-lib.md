---
layout: post
title: "[Lua] 使用第3方库时，如何导入路径"
date: 2017-07-29
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---

如果是一个 *.LUA 的文件， 里面用到了自己写的库， 或者第三方写的库， 但是你不想把它放到 lua 的安装目录里， 则在代码里面可以指定require搜索的路径。

```lua
package.path = '/usr/local/share/lua/5.1/?.lua;/home/resty/?.lua;'    --搜索lua模块
package.cpath = '/usr/local/lib/lua/5.1/?.so;'        --搜索so模块

package.path = package.path .. ";E:\\Lua\\lualogging\\src\\?.lua"
```
