---
layout: post
title: '[JavaScript] map reduce filter'
date: 2019-10-22
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

```
/*
map 把函数作用在数组的每一个元素上，并返回新的数组
*/
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
const newArray = arr.map(x => x * x);
console.log(newArray); // [1, 4, 9, 16, 25, 36, 49, 64, 81]
```

```
/*
reduce 中的函数接收两个参数
[x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4)
*/
const arr = [1, 2, 3];
const sumOfArray = arr.reduce((x, y) => x + y);
console.log(sumOfArray); // 6
```

```
/*
filter 也是一个常用的操作，它用于把 Array 的某些元素过滤掉，然后返回剩下的元素。

和 map() 类似，Array 的 filter() 也接收一个函数。和 map() 不同的是，filter() 把
传入的函数依次作用于每个元素，然后根据返回值是 true 还是 false 决定保留还是丢弃
该元素。
*/
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
const evenArry = arr.filter(x => x % 2 === 0); // 过滤出偶数
console.log(evenArry); // [ 2, 4, 6, 8 ]

const arr2 = ['A', '', 'B', null, undefined, 'C', '  '];
const validStringArray = arr2.filter(function(s) {
  return s && s.trim();
});
console.log(validStringArray); // [ 'A', 'B', 'C' ]

/*
数组去重
self[index] == element
self 就是 arr3 本身
去除重复元素依靠的是 indexOf 总是返回第一个元素的位置，
后续的重复元素位置与 indexOf 返回的位置不相等，因此被 filter 滤掉了。
*/
const arr3 = [1, 1, 1, 2, 2, 3, 3, 3, 3];
const result = arr3.filter(function(element, index, self) {
  return self.indexOf(element) === index;
});
console.log(result); // [ 1, 2, 3 ]
```
