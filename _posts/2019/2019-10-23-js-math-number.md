---
layout: post
title: '[JavaScript] Math 和 Number 常用方法'
date: 2019-10-23
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

## Math

```
console.log(Math.PI); // 3.141592653589793
console.log(Math.abs(-10.123)); // 10.123
console.log(Math.ceil(10.01)); // 11
console.log(Math.floor(10.01)); // 10
console.log(Math.round(10.01)); // 10
console.log(Math.round(10.9)); // 11
console.log(Math.max(1, 2, 3, 4)); // 4
console.log(Math.min(1, 2, 3, 4)); // 1
console.log(Math.random()); // 0.0626044010373974
console.log(Math.pow(2, 3)); // 8
console.log(Math.sqrt(9)); // 3
```

- abs：返回绝对值
- ceil：向上取整
- floor：向下取整
- round：四舍五入取整
- max：求最大值
- min：求最小值
- random：返回 0 到 1 之间的伪随机数
- pow(x, y)：返回 x 的 y 次幂
- sqrt(x)：返回 x 的平方根

## Number

```
console.log(Number.EPSILON); // 2.220446049250313e-16
console.log(Number.MAX_VALUE); // 1.7976931348623157e+308
console.log(Number.MIN_VALUE); // 5e-324
console.log(Number.MAX_SAFE_INTEGER); // 9007199254740991
console.log(Number.MIN_SAFE_INTEGER); // -9007199254740991
console.log(Number.POSITIVE_INFINITY); // Infinity
console.log(Number.NEGATIVE_INFINITY); // -Infinity
console.log(Number.NaN); // NaN
```

- Number.EPSILON：两个可表示(representable)数之间的最小间隔。
- Number.MAX_VALUE：能表示的最大正数。最小的负数是 `-MAX_VALUE`。
- Number.MIN_VALUE：能表示的最小正数即最接近 0 的正数 (实际上不会变成 0)。
  最大的负数是 `-MIN_VALUE`。
- Number.MAX_SAFE_INTEGER：JavaScript 中最大的安全整数 `(2^53 - 1)`。
- Number.MIN_SAFE_INTEGER：JavaScript 中最小的安全整数 `(-(2^53 - 1))`。
- Number.POSITIVE_INFINITY：特殊的正无穷大值，在溢出时返回该值。
- Number.NEGATIVE_INFINITY：特殊的负无穷大值，在溢出时返回该值。
- Number.NaN：特殊的“非数字”值。

## 参考链接

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math)

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number)
