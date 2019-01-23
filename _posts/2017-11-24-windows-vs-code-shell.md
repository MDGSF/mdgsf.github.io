---
layout: post
title:  "[Windows] VSCode 添加 mingw"
date:   2017-11-24
comments: true
categories: Windows
tags: [Windows]
description:
published: true
---

文件-->首选项-->设置-->用户设置

{
    "files.trimTrailingWhitespace": true,
    "typescript.check.npmIsInstalled": false,
    "terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
    "terminal.integrated.shellArgs.windows": [
        "--login",
        "--init-file",
        "C:\\Program Files\\Git\\etc\\profile"
    ],
    "terminal.external.windowsExec":"C:\\Program Files\\Git\\bin\\bash.exe"
}
