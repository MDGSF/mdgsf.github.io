---
layout: post
title: '[JavaScript] Object 使用'
date: 2019-10-22
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

```
const obj = {};

// set
obj.name = 'huangjian';
obj['age'] = 1024;

// get
console.log(`obj.name = ${obj.name}`); // huangjian
console.log(`obj['name'] = ${obj['name']}`); // huangjian

// delete
obj['willBeDeleted'] = true;
delete obj['willBeDeleted']; // method one
delete obj.willBeDeleted; // method two

// update
obj['name'] = 'huangping';
obj.age = 512;

console.log(obj); // { name: 'huangping', age: 512 }

// 遍历 object
// for in 会遍历整个原型链，所以只能加上 hasOwnProperty()
//
// 输出：
// name huangping
// age 512
var hasOwnProperty = Object.prototype.hasOwnProperty;
for (let key in obj) {
  if (hasOwnProperty.call(obj, key)) {
    console.log(key, obj[key]);
  }
}

// get all keys
// Object.prototype.bar = 1; 这行代码可以测试 Object.keys(obj) 是否会遍历原型链
const keys = Object.keys(obj);
console.log(keys); // [ 'name', 'age' ]

// 访问属性，[] 和 . 的两个区别

// 属性名不是一个有效的变量名
// obj.1234 = true; // SyntaxError
obj['1234'] = true; // valid

// 变量作为 key
const key = 'editor';
obj[key] = 'vim';
```
