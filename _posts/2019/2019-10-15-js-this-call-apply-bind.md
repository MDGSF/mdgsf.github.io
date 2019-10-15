---
layout: post
title: '[JavaScript] this,call,apply,bind 用法'
date: 2019-10-15
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

该文章中的代码测试环境为 `nodejs`，浏览器下行为可能略有不同。

## this

`this` 就只是一个指针，只能在函数内使用。函数在任何地方使用，都会有一个 `this`
指针，只是在不同的环境下，`this` 会指向不同的东西。

### this 指向 global

```
x = 1;
function test() {
  console.log(this.x);
}
test(); // 1
```

### this 指向对象

这种情况下和 `C++` 中的 `this` 是类似的。

```
var obj = {
  x: 2,
  test() {
    console.log(this.x);
  },
};
obj.test(); // 2
```

函数作为构造函数使用：

```
x = 1;
function test() {
  this.x = 2;
}
var obj = new test();
console.log(obj.x); // 2
console.log(x); // 1
```

## call, apply 和 bind 对比

`call()`，`apply()` 和 `bind()` 都是函数的一个方法。

它们都是用来修改 `this` 指针的指向的。

```
name = 'xiaowang';
age = 17;
var obj = {
  name: 'xiaozhang',
  objAge: this.age,
  myFun() {
    console.log(this.name + ' age:' + this.age);
  },
};
var db = {
  name: '德玛',
  age: 99,
};
obj.myFun.call(db); // 德玛 age:99
obj.myFun.apply(db); // 德玛 age:99
obj.myFun.bind(db)(); // 德玛 age:99
```

```
name = 'xiaowang';
age = 17;
var obj = {
  name: 'xiaozhang',
  objAge: this.age,
  myFun(from, to) {
    console.log(this.name + ' age:' + this.age, '来自 ' + from + ' 去往 ' + to);
  },
};
var db = {
  name: '德玛',
  age: 99,
};
obj.myFun.call(db, 'US', 'China'); // 德玛 age:99 来自 US 去往 China
obj.myFun.apply(db, ['US', 'China']); // 德玛 age:99 来自 US 去往 China
obj.myFun.bind(db, 'US', 'China')(); // 德玛 age:99 来自 US 去往 China
```

## call

`fun.call(thisArg, arg1, arg2, ...)`

**注意：**该方法的语法和作用与 `apply()` 方法类似，只有一个区别，就是 `call()`
方法接受的是**一个参数列表**，而 `apply()` 方法接受的是**一个包含多个参数的数组**。

```
function Product(name, price) {
  this.name = name;
  this.price = price;
}

function Food(name, price) {
  Product.call(this, name, price);
  this.category = 'food';
}

console.log(new Food('cheese', 5).name); // cheese
```

上面这个例子中的 `Product.call(this, name, price)` 把 `Product` 中的 `this`
指向了 `Food`。

```
function Product(name, price) {
  this.name = name;
  this.price = price;
}

function Food(name, price) {
  Product(name, price);
  this.category = 'food';
}

console.log(typeof new Food('cheese', 5).name); // undefined
console.log(name, price); // cheese 5
```

上面这个例子中 `Product` 函数中的 `this` 在运行时指向了 `global`。

## apply

`func.apply(thisArg, [argsArray])`

```
x = 1;
var obj = {
  x: 2,
  test() {
    console.log(this.x);
  },
};
obj.test(); // 2
obj.test.apply(); // 1
obj.test.apply(obj); // 2
```

`apply()` 参数为空时，`this` 指向 `global` 对象。

## bind

`function.bind(thisArg[,arg1[,arg2[, ...]]])`

`bind()` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被
`bind` 的第一个参数指定，其余的参数将作为新函数的参数供调用时使用。

```
var module = {
  x: 42,
  getX: function() {
    return this.x;
  },
};

// 这里的 this 指向 module
console.log(module.getX());

// 这里的 this 指向 global，由于 global 中没有 x，所以返回 undefined
const unboundGetX = module.getX;
let result = unboundGetX();
console.log(typeof result);

// 给 global 加上 x，
// 注意不能在 x 前面加上 var,const,let，加上了就不是 global 变量了
x = 123;
console.log(unboundGetX());

// bind 返回的函数 boundGetX 中的 this 就是指向 bind 的第一个参数。
const boundGetX = unboundGetX.bind(module);
console.log(boundGetX());
```

## 参考链接

[https://www.cnblogs.com/Shd-Study/p/6560808.html](https://www.cnblogs.com/Shd-Study/p/6560808.html)

[http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
