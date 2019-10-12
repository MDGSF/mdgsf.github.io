---
layout: post
title: '[JavaScript] prototype 和 __proto__'
date: 2019-10-12
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

1. `js` 会顺着 `__proto__` 一直向上找，直到 `Object` 根对象。
2. 只有 `class` 和 `function` 有 `prototype`。
3. `prototype` 是给 `new` 出来的对象使用的。
4. `class A{}` 这里的 `A` 是一个类。
5. `function B(){}` 这里的 `B` 也是一个类。
6. `const C = {}` 这里的 `C` 是一个对象，不是一个类。
7. 该文章把有 `prototype` 这个属性的，都称为一个类。
8. `class A{}` 只是一个 `function A(){}` 的语法糖。

### 测试代码

`test.js`

`node --inspect-brk=9229 test.js` [开启调试]({{ site.url }}/2019/10/11/js-debug)。

```
class A {
  constructor(key) {
    this.key = key;
  }

  f1() {
    console.log("I'm f1");
  }
}

function B(key) {
  this.key = key;
}

B.prototype = {
  f2: function() {
    console.log("I'm f2");
  },
};

const C = {
  key: 'CC',

  f3() {
    console.log("I'm f3");
  },
};

function D() {
  console.log("I'm D function");
}

const a1 = new A(10);
const a2 = new A(20);

const b1 = new B(10);
const b2 = new B(20);

const d1 = new D();

console.log('end');
```

### 参考链接

[https://stackoverflow.com/questions/9959727/proto-vs-prototype-in-javascript](https://stackoverflow.com/questions/9959727/proto-vs-prototype-in-javascript)
