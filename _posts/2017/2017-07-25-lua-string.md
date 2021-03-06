---
layout: post
title: "[Lua-pil] 字符串操作"
date: 2017-07-25
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---

读书笔记： Programming in Lua, 4th Edition.

### 替换字符串

```lua
> a = "one string"
> b = string.gsub(a, "one", "another")
> print(a, b)
one string      another string
>
```

### 计算字符串长度

```lua
> a = "hello"
> print(#a)
5
> print(#"good bye")
8
```

### 连接字符串

```lua
> "Hello " .. "World"
Hello World
> "result is " .. 3
result is 3
```

```lua
> a = "Hello"
> a .. " World"
Hello World
```

### 字符串表示

```lua
a = "a line"
b = 'another line'
```

They are equivalent; the only difference is that inside each kind of quote we can use the other quote without
escapes.

### 多行字符串

```lua
> page = [[
>> <html>
>> <head>
>> <title>An HTML Page</title>
>> </head>
>> <body>
>> <a href="http://www.lua.org">Lua</a>
>> </body>
>> </html>
>> ]]
> print(page)
<html>
<head>
<title>An HTML Page</title>
</head>
<body>
<a href="http://www.lua.org">Lua</a>
</body>
</html>
```

### 数字和字符串的转换

```lua
> print(10 .. 20)
1020
```

```lua
> "10" + 1
11.0
```

```lua
> tonumber("  -3  ")
-3
> tonumber(" 10e4 ")
100000.0
> tonumber("10e")
nil
```

By default, tonumber assumes decimal notation, but we can specify any base between 2 and 36 for the
conversion

```lua
> tonumber("100101", 2)
37
> tonumber("fff", 16)
4095
> tonumber("-ZZ", 36)
-1295
> tonumber("987", 8)
nil
```

```lua
> print(tostring(10) == "10")
true
```

## string 库

```lua
> string.len("abc")
3
> string.rep("abc", 3)
abcabcabc
> string.reverse("A Long Line!")
!eniL gnoL A
> string.lower("A Long Line!")
a long line!
> string.upper("A Long Line!")
A LONG LINE!
```

### 字符串比较

As a typical use, if we want to compare two strings regardless of case, we can write something like this:

**string.lower(a) < string.lower(b)**

### 截取字符串

The call string.sub(s, i, j) extracts a piece of the string s, from the i-th to the j-th character
inclusive. (The first character of a string has index 1.) We can also use negative indices, which count from
the end of the string: index -1 refers to the last character, -2 to the previous one, and so on. Therefore, the
call string.sub(s, 1, j) gets a prefix of the string s with length j; string.sub(s, j, -1)
gets a suffix of the string, starting at the j-th character; and string.sub(s, 2, -2) returns a copy
of the string s with the first and last characters removed:

```lua
> s = "[in brackets]"
> string.sub(s, 2, -2)
in brackets
> string.sub(s, 1, 3)
[in
> string.sub(s, -1, -1)
]
> string.sub(s, -2, -1)
s]
> print(s)
[in brackets]
> s = string.sub(s, 2, -2)
> print(s)
in brackets
```

### ASCII转换

```lua
> print(string.char(97))
a
> i = 99; print(string.char(i, i+1, i+2))
cde
> print(string.byte("abc"))
97
> print(string.byte("abc", 2))
98
> print(string.byte("abc", -1))
99
```

A call like string.byte(s, i, j) returns multiple values with the numeric representation of all
characters between indices i and j (inclusive):

```lua
> print(string.byte("abc", 1, 2))
97      98
```

A nice idiom is {string.byte(s, 1, -1)}, which creates a list with the codes of all characters in s.

### string.format

和C语言的printf是一样的。

```lua
> string.format("x = %d y = %d", 10, 20)
x = 10 y = 20
> string.format("x = %x", 200)
x = c8
> string.format("x = 0x%X", 200)
x = 0xC8
> string.format("x = %f", 200)
x = 200.000000
> tag, title = "h1", "a title"
> string.format("<%s>%s<%s>", tag, title, tag)
<h1>a title<h1>
> print(string.format("pi = %.4f", math.pi))
pi = 3.1416
> d = 5; m = 11; y = 1990
> print(string.format("%02d/%02d/%04d", d, m, y))
05/11/1990
```

### 字符串查找

```lua
> string.find("hello world", "wor")
7       9
> string.find("hello world", "war")
nil
```

### 字符串替换

```lua
> string.gsub("hello world", "l", ".")
he..o wor.d     3
> string.gsub("hello world", "ll", "..")
he..o world     1
> string.gsub("hello world", "a", ".")
hello world     0
```
