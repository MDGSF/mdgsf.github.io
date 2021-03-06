---
layout: post
title: "[Lua-pil] Object-Oriented Programming"
date: 2017-07-27
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---

读书笔记： Programming in Lua, 4th Edition.

```lua
Account = {
    balance = 0,
    withdraw = function(self, v)
        self.balance = self.balance - v
    end
}

function Account:deposit(v)
    self.balance = self.balance + v
end

-- 用.来调用函数的话，需要手动给self传值。
-- 用冒号来调用函数的话，可以省略self。
Account.deposit(Account, 200)
Account:deposit(200)

Account.withdraw(Account, 100)
Account:withdraw(100)
```

在上面的例子中，Account只是一个对象实例。

在C++中，一个类，可以生成多个对象实例。

在Lua中，没有类的概念，Lua是用metatable来模拟实现类的。

## Classes

if we have two objects A and B, all we have to do to make B a prototype for A is this:

```lua
setmetatable(A, {__index = B})
```

After that, A looks up in B for any operation that it does not have.

Let us go back to our example of a bank account. To create other accounts with behavior similar to Account,
we arrange for these new objects to inherit their operations from Account, using the __index
metamethod.

```lua
local mt = {__index = Account}
function Account.new (o)
    o = o or {} -- create table if user does not provide one
    setmetatable(o, mt)
    return o
end
```

After this code, what happens when we create a new account and call a method on it, like this?

```lua
a = Account.new{balance = 0}
a:deposit(100.00)
```

When we create the new account, a, it will have mt as its metatable. When we call
a:deposit(100.00), we are actually calling a.deposit(a, 100.00); the colon is only syntactic
sugar. However, Lua cannot find a "deposit" entry in the table a; hence, Lua looks into the __index
entry of the metatable. The situation now is more or less like this:

```lua
getmetatable(a).__index.deposit(a, 100.00)
```

The metatable of a is mt, and mt.__index is Account. Therefore, the previous expression evaluates
to this one:

```lua
Account.deposit(a, 100.00)
```

That is, Lua calls the original deposit function, but passing a as the self parameter. So, the new account
a inherited the function deposit from Account. By the same mechanism, it inherits all fields from
Account.

We can make two small improvements on this scheme. The first one is that we do not need to create a new
table for the metatable role; instead, we can use the Account table itself for that purpose. The second
one is that we can use the colon syntax for the new method, too. With these two changes, method new
becomes like this:

```lua
function Account:new (o)
    o = o or {}
    self.__index = self
    setmetatable(o, self)
    return o
end
```

Now, when we call Account:new(), the hidden parameter self gets Account as its value, we make
Account.__index also equal to Account, and set Account as the metatable for the new object. It
may seem that we do not gained much with the second change (the colon syntax); the advantage of using
self will become apparent when we introduce class inheritance, in the next section.

## Inheritance 继承

```lua
Account = {balance = 0}

function Account:new(o)
    o = o or {}
    self.__index = self
    setmetatable(o, self)
    return o
end

function Account:deposit(v)
    self.balance = self.balance + v
end

function Account:withdraw(v)
    if v > self.balance then error"insufficient funds" end
    self.balance = self.balance - v
end

SpecialAccount = Account:new()
s = SpecialAccount:new{limit = 1000.00}

function SpecialAccount:withdraw(v)
    if v - self.balance >= self.getLimit() then
        error"insufficient funds"
    end
    self.balance = self.balance - v
end

function SpecialAccount:getLimit()
    return self.limit or 0
end
```

<img src="{{ site.url }}/images/2017/07/29_01.jpg" alt="29_01" />

就像一个链表一样，通过metatable一层一层向上找。
