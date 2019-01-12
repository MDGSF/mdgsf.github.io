---
layout: post
title: "[Lua] _G[]全局变量"
date: 2017-07-26
author: mdgsf
comments: true
categories: lua
tags: lua
description:
published: true #default true
---


Lua中的变量，如果没有local关键字，全都是全局变量，Lua也是用Table来管理全局变量的，Lua把这些全局变量放在了一个叫“_G”的Table里。


```lua
> a = 1
> print(a)
1
> _G["a"]
1
> a = 2
> _G["a"]
2
```


打印出全局变量_G中的所有东西：

```lua
for n in pairs(_G) do 
    print(n)
end
```
