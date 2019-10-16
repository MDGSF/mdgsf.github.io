---
layout: post
title: '[JavaScript] closures 闭包'
date: 2019-10-16
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

## 例子

### 例 1

```
// filename: test.js
function sayHello(name) {
  var text = 'Hello ' + name;
  var say = function() {
    console.log(text);
  };
  say();
}
sayHello('Joe');
```

`node --inspect-brk=9229 test.js` 用 `chrome` 调试代码，断点设置在 `say();` 那一
行。

<img src="{{ site.url }}/images/2019/10/20191016_01.png" alt="20191016_01.png" />

如上图所示，可以看到图中黄色背景的部分。`say` 对象的 `[[Scopes]]` 数组中有两个
对象，第一个就是一个 `Closure`。这其实就是一个匿名对象，该 `Closure` 中只有一个
`text` 属性。

**结论：** `say()` 只是一个函数，不是 `Closure`。`Closure` 只是 `say()` 函数中的
一个匿名对象。

### 例 2

```
function sayHello2(name) {
  var text = 'Hello ' + name;
  var say = function() {
    console.log(text);
  };
  return say;
}
var say2 = sayHello2('Bob');
say2();
```

`node --inspect-brk=9229 test.js` 用 `chrome` 调试代码，断点设置在 `say2();` 那一
行。

<img src="{{ site.url }}/images/2019/10/20191016_02.png" alt="20191016_02.png" />

如上图所示，可以看到图中黄色背景的部分。结论和 `例 1` 是一样的。

### 例 3

```
function say667() {
  var num = 42;
  var say = function() {
    console.log(num);
  };
  num++;
  return say;
}
var sayNumber = say667();
sayNumber();
```

`node --inspect-brk=9229 test.js` 用 `chrome` 调试代码，断点设置在 `sayNumber();`
那一行。

<img src="{{ site.url }}/images/2019/10/20191016_03.png" alt="20191016_03.png" />

如上图所示，可以看到图中黄色背景的部分。这次有些不同，你会发现 `num` 的值是
`43` 而不是 `42`。这说明了什么呢？这说明了 `num` 不是复制进去了，这好像就是 `Closure`
一直引用着 `say667()` 函数的堆栈，导致了 `say667()` 函数结束了，堆栈也没有被释放。

### 例 4

```
var gLogNumber = null;
var gIncreaseNumber = null;
var gSetNumber = null;

function setupSomeGlobales() {
  var num = 42;
  gLogNumber = function() {
    console.log(num);
  };
  gIncreaseNumber = function() {
    num++;
  };
  gSetNumber = function(x) {
    num = x;
  };
}

setupSomeGlobales();
gIncreaseNumber();
gLogNumber(); // 43
gSetNumber(5);
gLogNumber(); // 5

var oldLog = gLogNumber;
setupSomeGlobales();
gLogNumber(); // 42

oldLog(); // 5
```

这个例子较为复杂，但是也很能说明问题，请务必自己动手调试。

`node --inspect-brk=9229 test.js` 用 `chrome` 调试代码，断点分别设置在
两次调用 `setupSomeGlobales();` 那一行的下一行。

<img src="{{ site.url }}/images/2019/10/20191016_04.png" alt="20191016_04.png" />

上图是在第一个断点处的截图。可以看到，一共生成了 2 个 `Closure`。如果你把
`gLogNumber`，`gIncreaseNumber` 和 `gSetNumber` 全部展开，你会发现这 3 个是
指向相同的 `Closure`。也就是说，执行了一次 `setupSomeGlobales();` 函数，生成
了 3 个函数，这 3 个函数中有一个指针指向了相同的 `Closure`。

<img src="{{ site.url }}/images/2019/10/20191016_05.png" alt="20191016_05.png" />

上图是在第 2 个断点处的截图。可以看到，重新执行一次 `setupSomeGlobales();`
函数，生成了新的 `Closure` 了，而旧的 `Closure` 由于 `oldLog` 还引用着，也还可以
看到。

`oldLog` 和 `gLogNumber` 也指向了两个不同的函数，这两个函数有着不同的
`Closure`。这说明了，在 `JavaScript` 中，你如果把一个函数内部定义在另一个函数
内部，每次在调用外部的函数时，内部的函数都会被重新创建。

### 例 5

这个例子的问题估计只要写过 `JavaScript` 的人都出错过。

这个例子需要知道 var,let,const 的区别。

```
function buildList(list) {
  var result = [];
  for (var i = 0; i < list.length; i++) {
    var item = 'item' + i;
    result.push(function() {
      console.log(item + ' ' + list[i]);
    });
  }
  return result;
}

function testList() {
  var fnlist = buildList([1, 2, 3]);
  for (var j = 0; j < fnlist.length; j++) {
    fnlist[j]();
  }
}

testList();
```

输出如下：

```
item2 undefined
item2 undefined
item2 undefined
```

`node --inspect-brk=9229 test.js` 用 `chrome` 调试代码，断点设置在 `fnlist[j]();`
那一行。

<img src="{{ site.url }}/images/2019/10/20191016_06.png" alt="20191016_06.png" />

如上图所示，我们可以看到，`fnlist` 这个数组中，`3` 个匿名函数，都引用了相同的
`Closure`。`Closure` 对象信息如下：

```
Closure {
  i: 3
  item: "item2"
  list: [1, 2, 3]
}
```

正因为 `3` 个匿名函数引用了相同的 `Closure`，所以输出了 `3` 行的
`item2 undefined`。

那要怎么修改呢？把 `var i` 和 `var item` 改成 `let i` 和 `let item` 就行了。
这样，变量 `i` 和变量 `item` 的作用域就由`函数级别`变成了`块作用域`了。

```
function buildList(list) {
  var result = [];
  for (let i = 0; i < list.length; i++) {
    let item = 'item' + i;
    result.push(function() {
      console.log(item + ' ' + list[i]);
    });
  }
  return result;
}

function testList() {
  var fnlist = buildList([1, 2, 3]);
  for (var j = 0; j < fnlist.length; j++) {
    fnlist[j]();
  }
}

testList();
```

输出如下：

```
item0 1
item1 2
item2 3
```

<img src="{{ site.url }}/images/2019/10/20191016_07.png" alt="20191016_07.png" />

上图中可以看到，`fnlist` 数组中的每个函数中的 `[[Scopes]]` 数组中都多出了两个
`Block` 属性的匿名对象。

那在没有 `let` 和 `const` 之前，是怎么修改来解决问题的呢？可以在加一个匿名函数。
代码如下：

```
function buildList(list) {
  var result = [];
  for (var i = 0; i < list.length; i++) {
    var item = 'item' + i;
    result.push(
      (function(item, i) {
        return function() {
          console.log(item + ' ' + list[i]);
        };
      })(item, i),
    );
  }
  return result;
}

function testList() {
  var fnlist = buildList([1, 2, 3]);
  for (var j = 0; j < fnlist.length; j++) {
    fnlist[j]();
  }
}

testList();
```

输出如下：

```
item0 1
item1 2
item2 3
```

<img src="{{ site.url }}/images/2019/10/20191016_08.png" alt="20191016_08.png" />

上图中可以看到，`fnlist` 数组中的每个函数中的 `[[Scopes]]` 数组中都多出了一个
`Closure` 属性的匿名对象。看起来似乎外层函数每多一个，`[[Scopes]]` 中的 `Closure`
数量就会增加。

可以在加一层看看，代码如下：

```
function buildList(list) {
  var result = [];
  for (var i = 0; i < list.length; i++) {
    var item = 'item' + i;
    result.push(
      (function(item, i) {
        return (function(i) {
          return function() {
            console.log(item + ' ' + list[i]);
          };
        })(i);
      })(item, i),
    );
  }
  return result;
}

function testList() {
  var fnlist = buildList([1, 2, 3]);
  for (var j = 0; j < fnlist.length; j++) {
    fnlist[j]();
  }
}

testList();
```

读者可自行调试查看结果。

### 例 6

```
function newClosure(someNum, someRef) {
  var num = someNum;
  var anArray = [1, 2, 3];
  var ref = someRef;
  return function(x) {
    num += x;
    anArray.push(num);
    console.log(
      'num: ' +
        num +
        '; anArray: ' +
        anArray.toString() +
        '; ref.someVar: ' +
        ref.someVar +
        ';',
    );
  };
}
const obj = {someVar: 4};
const fn1 = newClosure(4, obj);
const fn2 = newClosure(5, obj);
fn1(1); // num: 5; anArray: 1,2,3,5; ref.someVar: 4;
fn2(1); // num: 6; anArray: 1,2,3,6; ref.someVar: 4;

obj.someVar++;
fn1(2); // num: 7; anArray: 1,2,3,5,7; ref.someVar: 5;
fn2(2); // num: 8; anArray: 1,2,3,6,8; ref.someVar: 5;
```

这个例子中，`fn1` 和 `fn2` 两个函数内部都有独立的 `Closure`。

<img src="{{ site.url }}/images/2019/10/20191016_09.png" alt="20191016_09.png" />

### 例 7

那如果内部的函数没有用到外部的变量，这种情况下有闭包吗？

那让我们写段代码测试下。

```
function testOut() {
  var testInner = function() {
    console.log("I'm inner");
  };
  return testInner;
}

var f = testOut();
f();
```

`node --inspect-brk=9229 test.js` 用 `chrome` 调试代码，断点设置在 `f();`
那一行。

<img src="{{ site.url }}/images/2019/10/20191016_10.png" alt="20191016_10.png" />

可以看到上图中并没有闭包。

## 参考链接

[https://stackoverflow.com/questions/111102/how-do-javascript-closures-work](https://stackoverflow.com/questions/111102/how-do-javascript-closures-work)
