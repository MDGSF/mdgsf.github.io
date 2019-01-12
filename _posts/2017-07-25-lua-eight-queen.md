---
layout: post
title: "[Lua-pil] 八皇后"
date: 2017-07-25
author: mdgsf
comments: true
categories: lua
tags: lua
description:
published: true #default true
---

读书笔记： Programming in Lua, 4th Edition. 




```lua
N = 8  -- board size

-- check whether position (n,c) is free from attacks
function isplaceok(a , n , c)
    for i = 1, n - 1 do
        if (a[i] == c) or 
           (a[i] - i == c - n) or 
           (a[i] + i == c + n) then
            return false --place can be attacked
        end
    end
    return true --no attacks; place is ok
end

-- print a board
function printsolution( a )
    for i = 1, N do 
        for j = 1, N do 
            io.write(a[i] == j and "X" or "-", " ")
        end
        io.write("\n")
    end
    io.write("\n")
end

-- add to board 'a' all queens from 'n' to 'N'
function addqueen( a, n )
    if n > N then
        printsolution(a)
    else
        for c = 1, N do 
            if isplaceok(a, n, c) then
                a[n] = c 
                addqueen(a, n + 1)
            end
        end
    end
end

addqueen({}, 1)
```

