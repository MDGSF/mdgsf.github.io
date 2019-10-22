---
layout: post
title: '[JavaScript] Map 使用'
date: 2019-10-22
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

## Example

```
const myMap = new Map();

const keyObj = {};
const keyFunc = function() {};
const keyString = 'a string';

// set
myMap.set(keyObj, 'keyObj value');
myMap.set(keyFunc, 'keyFunc value');
myMap.set(keyString, 'keyString value');

// get size
console.log(myMap.size); // 3

// get
console.log(myMap.get(keyObj)); // keyObj value
console.log(myMap.get(keyFunc)); // keyFunc value
console.log(myMap.get(keyString)); // keyString value

// delete
myMap.set('willBeDeleted', true);
myMap.delete('willBeDeleted');

// update
const newKeyStringValue = 'new keyString value';
myMap.set(keyString, newKeyStringValue);

// has
console.log(myMap.has('notExists')); // false
console.log(myMap.has(keyString)); // true
```

### for of

```
const myMap = new Map();

myMap.set(0, 'zero');
myMap.set(1, 'one');

// 0 = zero
// 1 = one
for (let [key, value] of myMap) {
  console.log(key + ' = ' + value);
}

// 0
// 1
for (let key of myMap.keys()) {
  console.log(key);
}

// zero
// one
for (let value of myMap.values()) {
  console.log(value);
}

// 0 = zero
// 1 = one
for (let [key, value] of myMap.entries()) {
  console.log(key + ' = ' + value);
}

// 0 = zero
// 1 = one
myMap.forEach(function(value, key) {
  console.log(key + ' = ' + value);
});
```

### map and array

```
const kvArray = [['key1', 'value1'], ['key2', 'value2']];

const myMap = new Map(kvArray);

// array to map
console.log(myMap); // Map { 'key1' => 'value1', 'key2' => 'value2' }

// map to array
console.log(Array.from(myMap)); // [ [ 'key1', 'value1' ], [ 'key2', 'value2' ] ]

// map.keys to array
console.log(Array.from(myMap.keys())); // [ 'key1', 'key2' ]
```

### merge

```
const first = new Map([[1, 'one'], [2, 'two'], [3, 'three']]);

const second = new Map([[1, 'uno'], [2, 'dos']]);

// Map对象同数组进行合并时，如果有重复的键值，则后面的会覆盖前面的。
const merged = new Map([...first, ...second, [1, 'eins']]);

console.log(merged.get(1)); // eins
console.log(merged.get(2)); // dos
console.log(merged.get(3)); // three
```

## 参考链接

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map)
