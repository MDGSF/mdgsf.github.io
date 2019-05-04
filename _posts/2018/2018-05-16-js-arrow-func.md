---
layout: post
title:  "[Web][Javascript] Arrow function"
date:   2018-05-16
comments: true
categories: Web
tags: [Js,Javascript]
description: ""
published: true
---

```js
// f1 is the same with f2
var f1 = v => v;
var f2 = function(v) {
    return v
};
console.log(f1(2));

// f3 is the same with f4
var f3 = () => 5;
var f4 = function() {
    return 5;
};
console.log(f3());


// sum1 is the same with sum2
var sum1 = (num1, num2) => num1 + num2;
var sum2 = function(num1, num2) {
    return num1 + num2;
};
console.log(sum1(1, 2));
```
