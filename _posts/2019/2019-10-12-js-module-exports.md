---
layout: post
title: '[JavaScript] exports 和 module.exports'
date: 2019-10-12
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ''
published: true
---

### 例子

module.js

```
const a = {};
const b = {};
module.exports = a;
```

main.js

```
const a = require('./module.js');
```

1. `exports` 和 `module.exports` 其实就是两个指针。
2. `exports` 和 `module.exports` 默认指向同一个对象 `{}`。
3. `require` 返回的是 `module.exports` 指向的内容。
4. `exports = a;` 这个代码相当于 `exports` 指向了一个新的东西，和
   `module.exports` 指向的东西不一样了。
5. `module.exports = a;` 就是 `module.exports` 指向了 `a`，那么 `require` 就会
   返回 `a`。
6. `module.exports = {a, b};` 就是 `module.exports` 指向了新的对象，这个对象里面
   包含 `a` 和 `b` 两个属性，那么 `require` 就会返回这个新的对象。
7. 那 `exports` 看上去好像没什么用啊？？是的，确实没用，估计是为了可以少打几个
   字母吧。
8. 建议直接把 `exports` 忘记了，只使用 `module.exports`。

### 例子 2

module.js

```
const a = {};
const b = {};
module.exports = {
  a,
  b,
};
```

main.js

```
const m = require('./module.js');
const {a, b} = require('./module.js');
const {a} = require('./module.js');
const {b} = require('./module.js');
```

`const m = require('./module.js');` 是获取到 `module.exports` 返回的新的对象，
这个新的对象里面包含 `a` 和 `b` 两个属性，并把返回的对象保存到变量 `m`。

`const {a, b} = require('./module.js');` 是把 `require` 返回的对象`解构`了，分别
取出其中的 `a` 和 `b` 属性所对应的值，并分别保存到 `a` 和 `b` 两个变量。

`const {a} = require('./module.js');` 是把 `require` 返回的对象`解构`了，只
取出其中的 `a` 属性所对应的值，并保存到 `a` 变量。

`const {b} = require('./module.js');` 是把 `require` 返回的对象`解构`了，只
取出其中的 `b` 属性所对应的值，并保存到 `b` 变量。
