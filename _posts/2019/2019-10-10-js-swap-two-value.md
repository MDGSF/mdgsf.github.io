---
layout: post
title: '[JavaScript] 交换两个变量的值'
date: 2019-10-10
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true #default true
---

```
a = 1;
b = 2;

b = [a, (a = b)][0];

// or use destructuring
// [b] = [a, (a = b)];

console.log(a, b);
```
