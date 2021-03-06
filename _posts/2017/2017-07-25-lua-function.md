---
layout: post
title: "[Lua-pil] Function"
date: 2017-07-25
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---

读书笔记： Programming in Lua, 4th Edition.

## 正常函数调用

```lua
print(8*9, 9/8)
a = math.sin(3) + math.cos(10)
print(os.date())
```

```lua
-- add the elements of sequence 'a'
function add (a)
    local sum = 0
    for i = 1, #a do
        sum = sum + a[i]
    end
    return sum
end
```

if the function has one single argument and that argument is either a literal string or a table constructor,
then the parentheses are optional:

如果函数只有一个参数，并且这个参数是字符串或者是table的话，函数的括号()可以省略。

```lua
print "Hello World"     <-->    print("Hello World")
dofile 'a.lua'          <-->    dofile ('a.lua')
print [[a multi-line    <-->    print([[a multi-line
      message]]                     message]])
f{x=10, y=20}           <-->    f({x=10, y=20})
type{}                  <-->    type({})
```

We can call a function with a number of arguments different from its number of parameters. Lua adjusts
the number of arguments to the number of parameters by throwing away extra arguments and supplying
nils to extra parameters. For instance, consider the next function:

函数调用时提供的参数可以和函数定义时的参数个数不同。不足的用nil补全，多余的话会被自动忽略。

```lua
function f (a, b)
    print(a, b)
end

f()           --> nil nil
f(3)          --> 3 nil
f(3, 4)       --> 3 4
f(3, 4, 5)    --> 3 4 (5 is discarded)
```

```lua
function incCount (n)
    n = n or 1
    globalCounter = globalCounter + n
end
```

## Multiple Results

```lua
function maximum (a)
    local mi = 1 -- index of the maximum value
    local m = a[mi] -- maximum value
    for i = 1, #a do
        if a[i] > m then
            mi = i; m = a[i]
        end
    end
    return m, mi -- return the maximum and its index
end
print(maximum({8,10,23,12,5})) --> 23 3
```

Lua always adjusts the number of results from a function to the circumstances of the call.

When we call a function as a statement, Lua discards all results from the function.

When we use a call as an expression (e.g., the operand of an addition), Lua keeps only the first result.

We get all results only when the call is the last (or the only) expression in a list of expressions.

```lua
function foo0 () end                  -- returns no results
function foo1 () return "a" end       -- returns 1 result
function foo2 () return "a", "b" end  -- returns 2 results
```

```lua
x, y = foo2()                 -- x="a", y="b"
x = foo2()                    -- x="a", "b" is discarded
x, y, z = 10, foo2()          -- x=10, y="a", z="b"
```

```lua
x,y = foo0() -- x=nil, y=nil
x,y = foo1() -- x="a", y=nil
x,y,z = foo2() -- x="a", y="b", z=nil
```

Remember that multiple results only happen when the call is the last (or only) expression in a list. A
function call that is not the last element in the list always produces exactly one result:

```lua
x,y = foo2(), 20 -- x="a", y=20 ('b' discarded)
x,y = foo0(), 20, 30 -- x=nil, y=20 (30 is discarded)
```

```lua
print(foo0()) --> (no results)
print(foo1()) --> a
print(foo2()) --> a b
print(foo2(), 1) --> a 1
print(foo2() .. "x") --> ax (see next)
```

A constructor also collects all results from a call, without any adjustments:

```lua
t = {foo0()} -- t = {} (an empty table)
t = {foo1()} -- t = {"a"}
t = {foo2()} -- t = {"a", "b"}
```

As always, this behavior happens only when the call is the last expression in the list; calls in any other
position produce exactly one result:

```lua
t = {foo0(), foo2(), 4} -- t[1] = nil, t[2] = "a", t[3] = 4
```

We can force a call to return exactly one result by enclosing it in an extra pair of parentheses:

```lua
print((foo0())) --> nil
print((foo1())) --> a
print((foo2())) --> a
```

## Variadic Functions 可变参数

```lua
function add(...)
    local s = 0
    for _, v in ipairs{...} do
        s = s + v
    end
    return s
end
print(add(3, 4, 10, 25, 12)) --> 54
```

Lua offers the function table.pack.1 This function receives any number of arguments and returns a
new table with all its arguments (just like {...}), but this table has also an extra field "n", with the total
number of arguments.

```lua
function nonils(...)
    local arg = table.pack(...)
    for i = 1, arg.n do
        if arg[i] == nil then
            return false
        end
    end
    return true
end

print(nonils(2,3,nil)) --> false
print(nonils(2,3)) --> true
print(nonils()) --> true
print(nonils(nil)) --> false
```

A call to select
has always one fixed argument, the selector, plus a variable number of extra arguments. If the selector is
a number n, select returns all arguments after the n-th argument; otherwise, the selector should be the
string "#", so that select returns the total number of extra arguments.

```lua
print(select(1, "a", "b", "c")) --> a b c
print(select(2, "a", "b", "c")) --> b c
print(select(3, "a", "b", "c")) --> c
print(select("#", "a", "b", "c")) --> 3
```

```lua
function add (...)
    local s = 0
        for i = 1, select("#", ...) do
            s = s + select(i, ...)
        end
    return s
end
```

## table.unpack

A special function with multiple returns is table.unpack. It takes a list and returns as results all elements
from the list:

```lua
print(table.unpack{10,20,30}) --> 10 20 30
a,b = table.unpack{10,20,30} -- a=10, b=20, 30 is discarded
```

As the name implies, table.unpack is the reverse of table.pack. While pack transforms a parameter
list into a real Lua list (a table), unpack transforms a real Lua list (a table) into a return list, which
can be given as the parameter list to another function.

```lua
function unpack (t, i, n)
    i = i or 1
    n = n or #t
    if i <= n then
        return t[i], unpack(t, i + 1, n)
    end
end
```

## Proper Tail Calls

尾调用：也就是在一个函数A的最后调用一个函数B，因为函数A已经执行完了，所以函数B可以重复利用A的栈空间，提高效率。
