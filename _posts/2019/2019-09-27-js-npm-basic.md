---
layout: post
title: "[JavaScript] npm 基础使用"
date: 2019-09-27
author: mdgsf
comments: true
categories: Web
tags: [js, javascript]
description: ""
published: true #default true
---

### 根据 package.json 来安装相应的模块

`npm install` 根据 `package.json` 文件中的 `dependencies` 和
`devDependencies` 来安装模块。

`npm install --production` 根据 `package.json` 文件中的 `dependencies` 来安装
模块。

### 安装模块到项目目录下

并写入 package.json 文件的 `dependencies` 里面。

```
npm install moduleName
```

### 安装模块到全局

具体安装到哪里，可以用 `npm config get prefix` 查看。

```
npm install -g moduleName
```

### 安装模块到项目目录下

并写入 package.json 文件的 `dependencies` 里面。

```
npm install --save-prod moduleName
```

### 安装模块到项目目录下

并写入 package.json 文件的 `devDependencies` 里面。

```
npm install --save-dev moduleName
```

一些开发时会用到的，但是部署时用不到的，就用 `-save-dev`，比如 `eslint`。

### 初始化当前目录

新建项目的时候，使用这个命令会在当前目录下创建 `package.json` 文件。

```
npm init
```

