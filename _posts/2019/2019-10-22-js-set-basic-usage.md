---
layout: post
title: '[JavaScript] Set 使用'
date: 2019-10-22
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

## Example

### 使用 Set 对象

```
const mySet = new Set();

// add
mySet.add(1);
mySet.add(5);
mySet.add(5);
mySet.add('some text');
const o = {a: 1, b: 2};
mySet.add(o);
console.log(mySet); // Set { 1, 5, 'some text', { a: 1, b: 2 } }

// has
console.log(mySet.has(1)); // true
console.log(mySet.has(3)); // false
console.log(mySet.has(5)); // true
console.log(mySet.has(Math.sqrt(25))); // true
console.log(mySet.has('Some Text'.toLowerCase())); // true
console.log(mySet.has(o)); // true
console.log(mySet.has({a: 1, b: 2})); // false, 这是一个新的对象

// size
console.log(mySet.size); // 4

// delete
mySet.delete(5);
console.log(mySet.has(5)); // false
console.log(mySet.size); // 3
console.log(mySet); // Set { 1, 'some text', { a: 1, b: 2 } }

// clear
mySet.clear();
console.log(mySet); // Set {}
```

### 迭代 Set

```
const mySet = new Set();

mySet.add(1);
mySet.add(5);
mySet.add('some text');

// 1 5 some text
for (let item of mySet) {
  console.log(item);
}

// 1 5 some text
for (let item of mySet.keys()) {
  console.log(item);
}

// 1 5 some text
for (let item of mySet.values()) {
  console.log(item);
}

// 1 1
// 5 5
// some text some text
for (let [key, value] of mySet.entries()) {
  console.log(key, value);
}

// [ 1, 1  ]
// [ 5, 5  ]
// [ 'some text', 'some text'  ]
for (let item of mySet.entries()) {
  console.log(item);
}
```

### Set 和 Array 互相转换

```
// 利用 Set 给数组去重
const arr = [1, 1, 1, 2, 2, 3, 3, 3, 3];

const mySet = new Set(arr);
const newArray = Array.from(mySet);
console.log(newArray); // [ 1, 2, 3 ]

const newArray2 = [...new Set(arr)];
console.log(newArray2); // [ 1, 2, 3 ]
```

### 基本集合操作

```
function isSuperSet(set, subset) {
  for (let elem of subset) {
    if (!set.has(elem)) {
      return false;
    }
  }
  return true;
}

function union(setA, setB) {
  const _union = new Set(setA);
  for (let elem of setB) {
    _union.add(elem);
  }
  return _union;
}

function intersection(setA, setB) {
  const _intersection = new Set();
  for (let elem of setB) {
    if (setA.has(elem)) {
      _intersection.add(elem);
    }
  }
  return _intersection;
}

function difference(setA, setB) {
  const _difference = new Set(setA);
  for (let elem of setB) {
    _difference.delete(elem);
  }
  return _difference;
}

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 3]);
const setC = new Set([3, 4, 5, 6]);

console.log(isSuperSet(setA, setB)); // true
console.log(union(setA, setC)); // Set { 1, 2, 3, 4, 5, 6 }
console.log(intersection(setA, setC)); // Set { 3, 4 }
console.log(difference(setA, setC)); // Set { 1, 2 }
```

## 参考链接

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)
