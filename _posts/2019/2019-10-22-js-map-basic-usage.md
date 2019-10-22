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

## Objects 和 maps 的比较

Objects 和 Maps 类似的是，它们都允许你按键存取一个值、删除键、检测一个键是否绑定了值。因此（并且也没有其他内建的替代方式了）过去我们一直都把对象当成 Maps 使用。不过 Maps 和 Objects 有一些重要的区别，在下列情况里使用 Map 会是更好的选择：

- 一个 Object 的键只能是字符串或者 Symbols，但一个 Map 的键可以是任意值，包括函数、对象、基本类型。
- Map 中的键值是有序的，而添加到对象中的键则不是。因此，当对它进行遍历时，Map 对象是按插入的顺序返回键值。
- 你可以通过 size 属性直接获取一个 Map 的键值对个数，而 Object 的键值对个数只能手动计算。
- Map 可直接进行迭代，而 Object 的迭代需要先获取它的键数组，然后再进行迭代。
- Object 都有自己的原型，原型链上的键名有可能和你自己在对象上的设置的键名产生冲突。虽然 ES5 开始可以用 map = Object.create(null) 来创建一个没有原型的对象，但是这种用法不太常见。
- Map 在涉及频繁增删键值对的场景下会有些性能优势。

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

// clear all data
myMap.clear();
console.log(myMap); // Map {}
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
