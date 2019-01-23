---
layout: post
title: "[Lua-pil] Filling Some Gaps"
date: 2017-07-26
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---

读书笔记： Programming in Lua, 4th Edition. 



## do-end

do-end 就像是一个花括号，用来构成一个作用域。

```lua
> local x1
> do
>> local x1 = 1
>> print(x1)
>> end
1
> print(x1)
nil
```


## if-then-elseif-else-end

```lua
if op == "+" then
    r = a + b
elseif op == "-" then
    r = a - b
elseif op == "*" then
    r = a * b
elseif op == "/" then
    r = a / b
else
    error("invalid operation")
end
```


## while

```lua
local i = 1
while a[i] do
    print(a[i])
    i = i + 1
end
```

## repeat-until

至少执行一次，就是C语言中的do-while

```lua
-- print the first non-empty input line
local line
repeat
    line = io.read()
until line ~= ""
print(line)
```

Differently from most other languages, in Lua the scope of a local variable declared inside the loop includes
the condition:

```lua
-- computes the square root of 'x' using Newton-Raphson method
local sqr = x / 2
repeat
    sqr = (sqr + x/sqr) / 2
    local error = math.abs(sqr^2 - x)
until error < x/10000 -- local 'error' still visible here
```

## for

The **for** statement has two variants: the **numerical** for and the **generic** for.

### Numerical for

This loop will execute something for each value of var from exp1 to exp2, using exp3 as the step
to increment var. This third expression is optional; when absent, Lua assumes one as the step value.

```lua
for var = exp1, exp2, exp3 do
    something
end
```

If we want a loop without an upper limit, we can use the constant math.huge:

```lua
for i = 1, math.huge do
    if (0.3*i^3 - 20*i^2 - 500 >= 0) then
        print(i)
        break
    end
end
```


### Generic for











