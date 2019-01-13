---
layout: post
title:  "[Linux] apt use sock5 proxy"
date:   2018-05-19
comments: true
categories: Linux
tags: linux, apt
description:
published: true
---

/etc/apt/apt.conf

Acquire::socks::proxy "socks5://127.0.0.1:1080";

