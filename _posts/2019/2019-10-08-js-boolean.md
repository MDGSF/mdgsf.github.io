---
layout: post
title: "[JavaScript] boolean 布尔值"
date: 2019-10-08
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ""
published: true #default true
---

```
if ({}) {
  console.log('{} is true');
} else {
  console.log('{} is false');
}

if (true) {
  console.log('true is true');
} else {
  console.log('true is false');
}

if (false) {
  console.log('false is true');
} else {
  console.log('false is false');
}

if (null) {
  console.log('null is true');
} else {
  console.log('null is false');
}

if (undefined) {
  console.log('undefined is true');
} else {
  console.log('undefined is false');
}

if (NaN) {
  console.log('NaN is true');
} else {
  console.log('NaN is false');
}

if ('') {
  console.log('empty string(\'\') is true');
} else {
  console.log('empty string(\'\') is false');
}

if (0) {
  console.log('0 is true');
} else {
  console.log('0 is false');
}

if (-0) {
  console.log('-0 is true');
} else {
  console.log('-0 is false');
}

if (1) {
  console.log('1 is true');
} else {
  console.log('1 is false');
}

if (-1) {
  console.log('-1 is true');
} else {
  console.log('-1 is false');
}

if ('true') {
  console.log('\'true\' is true');
} else {
  console.log('\'true\' is false');
}

if ('false') {
  console.log('\'false\' is true');
} else {
  console.log('\'false\' is false');
}

if ('0') {
  console.log('\'0\' is true');
} else {
  console.log('\'0\' is false');
}

if ('1') {
  console.log('\'1\' is true');
} else {
  console.log('\'1\' is false');
}
```

输出：

```
{} is true
true is true
false is false
null is false
undefined is false
NaN is false
empty string('') is false
0 is false
-0 is false
1 is true
-1 is true
'true' is true
'false' is true
'0' is true
'1' is true
```

