---
layout: post
title: '[JavaScript] 交换两个变量的值'
date: 2019-10-10
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

```
a = 1;
b = 2;

// method 1
[a, b] = [b, a]

// method 2
// b = [a, (a = b)][0];

// method 3
// or use destructuring
// [b] = [a, (a = b)];

console.log(a, b);
```
