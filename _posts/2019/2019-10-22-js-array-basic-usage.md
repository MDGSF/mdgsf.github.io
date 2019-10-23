---
layout: post
title: '[JavaScript] Array 基础使用'
date: 2019-10-22
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

### map

map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回
的结果。

#### 语法

```
@param callback: 生成新数组元素的函数，它可以接收三个参数：
    参数1 currentValue: 用于测试的当前值。
    参数2 index(可选): 用于测试的当前值的索引。
    参数3 array(可选): 调用 every 的当前数组。
@param thisArg(可选): 执行 callback 时使用的 this 值。
@return: 回调函数的结果组成了新数组的每一个元素。
var new_array = arr.map(function callback(currentValue[, index[, array]]) {
 // Return element for new_array
}[, thisArg])
```

```
/*
map 把函数作用在数组的每一个元素上，并返回新的数组
*/
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
const newArray = arr.map(x => x * x);
console.log(newArray); // [1, 4, 9, 16, 25, 36, 49, 64, 81]
```

```
const numbers = [1, 4, 9];
const roots = numbers.map(Math.sqrt);
console.log(numbers); // [ 1, 4, 9 ]
console.log(roots); // [ 1, 2, 3 ]
```

```
// 在一个 String 上使用 map 方法获取字符串中每个字符所对应的 ASCII 码组成的数组
const map = Array.prototype.map;
const a = map.call('Hello World', function(x) {
  return x.charCodeAt(0);
});
console.log(a);
```

### reduce

reduce() 方法对数组中的每个元素执行一个由您提供的 reducer 函数(升序执行)，
将其结果汇总为单个返回值。

#### 语法

```
@param callback: 执行数组中每一个值的函数，它可以接收 4 个参数：
    参数1 accumulator: 累加器累计回调的返回值。
    参数1 currentValue: 数组中正在处理的元素。
    参数3 index(可选): 用于测试的当前值的索引。
    参数4 array(可选): 调用 every 的当前数组。
@param initialValue(可选): 作为第一次调用 callback 函数时的第一个参数的值。
@return: 函数累计处理的结果。
arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])
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
// 累加对象数组中包含的值
var initialValue = 0;
var sum = [{x: 1}, {x: 2}, {x: 3}].reduce(
  (accumulator, currentValue) => accumulator + currentValue.x,
  initialValue,
);
console.log(sum); // 6
```

```
// 将二维数组转化为一维
var flattened = [[0, 1], [2, 3], [4, 5]].reduce(
  (acc, cur) => acc.concat(cur),
  [],
);
console.log(flattened); // [0, 1, 2, 3, 4, 5]
```

```
// 计算数组中每个元素出现的次数
var names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice'];

var countedNames = names.reduce(function(allNames, name) {
  if (name in allNames) {
    allNames[name]++;
  } else {
    allNames[name] = 1;
  }
  return allNames;
}, {});

console.log(countedNames); // { Alice: 2, Bob: 1, Tiff: 1, Bruce: 1 }
```

```
// 使用扩展运算符和initialValue绑定包含在对象数组中的数组
var friends = [
  {
    name: 'Anna',
    books: ['Bible', 'Harry Potter'],
    age: 21,
  },
  {
    name: 'Bob',
    books: ['War and peace', 'Romeo and Juliet'],
    age: 26,
  },
  {
    name: 'Alice',
    books: ['The Lord of the Rings', 'The Shining'],
    age: 18,
  },
];

var allbooks = friends.reduce((acc, cur) => [...acc, ...cur.books], [
  'Alphabet',
]);

console.log(allbooks);
// [
//   'Alphabet',
//   'Bible',
//   'Harry Potter',
//   'War and peace',
//   'Romeo and Juliet',
//   'The Lord of the Rings',
//   'The Shining',
// ];
```

```
// 数组去重 1
var myArray = ['a', 'b', 'a', 'b', 'c', 'e', 'e', 'c', 'd', 'd', 'd', 'd'];
var myOrderedArray = myArray.reduce(function(accumulator, currentValue) {
  if (accumulator.indexOf(currentValue) === -1) {
    accumulator.push(currentValue);
  }
  return accumulator;
}, []);
console.log(myOrderedArray); // [ 'a', 'b', 'c', 'e', 'd' ]
```

```
// 数组去重 2
let arr = [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4];
let result = arr.sort().reduce((init, current) => {
  if (init.length === 0 || init[init.length - 1] !== current) {
    init.push(current);
  }
  return init;
}, []);
console.log(result); // [ 1, 2, 3, 4, 5 ]
```

```
// 数组去重 3
let arr = [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4];
let result = Array.from(new Set(arr));
console.log(result); // [ 1, 2, 3, 5, 4 ]
```

### filter

filter() 方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。

#### 语法

```
@param callback: 用来测试每个元素的函数。返回 true 表示该元素通过测试，
    保留该元素，false 则不保留。它可以接收三个参数：
    参数1 element: 用于测试的当前值。
    参数2 index(可选): 用于测试的当前值的索引。
    参数3 array(可选): 调用 every 的当前数组。
@param thisArg(可选): 执行 callback 时使用的 this 值。
@return: 一个新的、由通过测试的元素组成的数组，如果没有任何数组元素通过测试，
    则返回空数组。
var newArray = arr.filter(callback(element[, index[, array]])[, thisArg])
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

```
// 筛选排除所有较小的值
function isBigEnough(element) {
  return element >= 10;
}
var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
console.log(filtered); // [ 12, 130, 44 ]
```

### every

`every()` 方法测试一个数组内的所有元素是否都能通过某个指定函数的测试。
它返回一个布尔值。

#### 语法

```
@param callback: 用来测试每个元素的函数，它可以接收三个参数：
    参数1 element: 用于测试的当前值。
    参数2 index(可选): 用于测试的当前值的索引。
    参数3 array(可选): 调用 every 的当前数组。
@param thisArg(可选): 执行 callback 时使用的 this 值。
@return: 如果回调函数的每一次返回都为 truthy 值，返回 true ，否则返回 false。
arr.every(callback[, thisArg])
```

```
// 检测数组中的所有元素是否都大于 10。
function isBigEnough(element, index, array) {
  return element >= 10;
}
console.log([12, 5, 8, 130, 44].every(isBigEnough)); // false
console.log([12, 54, 18, 130, 44].every(isBigEnough)); // true
console.log([12, 5, 8, 130, 44].every(x => x >= 10)); // false
console.log([12, 54, 18, 130, 44].every(x => x >= 10)); // true
```

## 参考链接

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/every](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/every)
