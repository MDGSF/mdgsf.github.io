---
layout: post
title: "[Lua] logging日志库使用"
date: 2017-07-29
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---


<a href="https://github.com/Neopallium/lualogging" target="_blank">https://github.com/Neopallium/lualogging</a>


## 实例1

日志库就是 lualogging，放在 E:\\Lua 这个目录下面。

```lua
package.path = package.path .. ";E:\\Lua\\lualogging\\src\\?.lua"

require("logging")

local logger = logging.new(
    function(self, level, message)
        print(level, message)
        return true
    end
)

-- logger:setLevel(logging.WARN)

logger:log(logging.INFO, "sending email")
logger:info("info log.")
logger:warn("warn log.")
logger:error("error log.")


local tab = { a = 1, b = 2, c = {1,2,3,4,5,6}}
logger:debug(tab)

logger:info("val1 = '%s', val2 = %d", "string value", 1234)
```


## 实例2 输出日志到文件中

```lua
package.path = package.path .. ";E:\\Lua\\lualogging\\src\\?.lua"
require("logging")
require("logging.file")

local logger = logging.file("test_%s.log", "%Y-%m-%d")

logger:info("logging.file test")
logger:debug("debug...")
logger:error("error!")
```


