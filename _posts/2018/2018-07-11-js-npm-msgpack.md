---
layout: post
title: "[Web] javascript npm msgpack"
date: 2018-07-11
author: mdgsf
comments: true
categories: Web
tags: [Js]
description: ""
published: true #default true
---

npm install
gyp WARN EACCES user "root" does not have permission to access the dev dir "/root/test/s5_repo/node_modules/msgpack/.node-gyp/8.11.1"
gyp WARN EACCES attempting to reinstall using temporary dev dir "/root/test/s5_repo/node_modules/msgpack/.node-gyp"
....

### 解决方法

npm install cnpm -g --registry=https://registry.npm.taobao.org
rm -rf node_modules/msgpack/
cnpm install
