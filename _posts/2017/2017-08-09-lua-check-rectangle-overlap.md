---
layout: post
title: "[Lua-love2d] 检测两个2D平面上2个矩形是否重叠"
date: 2017-08-09
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---

```lua
function collisions.check_rectangles_overlap(a, b)
    local overlap = false
    if not(
        a.x + a.width < b.x or
        b.x + b.width < a.x or
        a.y + a.height < b.y or
        b.y + b.height < a.y
         ) then
        overlap = true
    end
    return overlap
end
```
