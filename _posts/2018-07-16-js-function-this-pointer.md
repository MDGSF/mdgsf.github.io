---
layout: post
title: "[Web] javascript function this pointer"
date: 2018-07-16
author: mdgsf
comments: true
categories: Web
tags: [Js]
description: ""
published: true #default true
---

```javascript
var obj = {
    birth: 1990,

    // 由于JavaScript函数对this绑定的错误处理，下面的例子无法得到预期结果
    getAge1: function () {
        var b = this.birth; // 1990
        var fn = function () {
            return new Date().getFullYear() - this.birth; // this指向window或undefined
        };
        return fn();
    },

    // 箭头函数完全修复了this的指向，this总是指向词法作用域，也就是外层调用者obj
    getAge2: function () {
        var b = this.birth; // 1990
        var fn = () => new Date().getFullYear() - this.birth; // this指向obj对象
        return fn();
    },

    // 由于this在箭头函数中已经按照词法作用域绑定了，所以，用call()或者apply()调用箭头函数时，无法对this进行绑定，即传入的第一个参数被忽略
    getAge3: function (year) {
        var b = this.birth; // 1990
        var fn = (y) => y - this.birth; // this.birth仍是1990
        return fn.call({birth:2000}, year);
    }
};
console.log(obj.getAge1());      // NaN
console.log(obj.getAge2());      // 28
console.log(obj.getAge3(2018));  // 28
```
