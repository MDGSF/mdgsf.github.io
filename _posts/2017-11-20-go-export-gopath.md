---
layout: post
title:  "[GO] GoPath管理"
date:   2017-11-20
comments: true
categories: GO
tags: [Go]
description: ""
published: true
---


## build.sh

```bash
#!/bin/bash
WDIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"
export GOPATH=$WDIR
go install accserver
go install dfmjserver
go install dtmjserver
```
