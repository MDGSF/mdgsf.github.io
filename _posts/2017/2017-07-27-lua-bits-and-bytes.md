---
layout: post
title: "[Lua-pil] Bits and Bytes"
date: 2017-07-27
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---

读书笔记： Programming in Lua, 4th Edition. 

## Bitwise Operators

```lua
> string.format("%x", 0xff & 0xabcd) --> cd
> string.format("%x", 0xff | 0xabcd) --> abff
> string.format("%x", 0xaaaa ~ -1) --> ffffffffffff5555
> string.format("%x", ~0) --> ffffffffffffffff
```

Negative displacements shift in the other direction, that is, a >> n is the same as a << -n:

```lua
> string.format("%x", 0xff << 12) --> ff000
> string.format("%x", 0xff >> -12) --> ff000
```

If the displacement is equal to or larger than the number of bits in the integer representation (64 in Standard
Lua, 32 in Small Lua), the result is zero, as all bits are shifted out of the result:

```lua
> string.format("%x", -1 << 80) --> 0
```








