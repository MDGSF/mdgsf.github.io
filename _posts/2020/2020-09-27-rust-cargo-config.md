---
layout: post
title:  "[Rust] cargo 源配置文件"
date:   2020-09-27
comments: true
categories: Rust
tags: [Rust, cargo]
description:
published: true
---

```toml
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
replace-with = 'sjtu'

# rustcc 1号源
[source.rustcc]
registry="git://crates.rustcc.com/crates.io-index"

# rustcc 2号源
[source.rustcc2]
registry="git://crates.rustcc.cn/crates.io-index"

# 清华大学
[source.tuna]
registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"

# 中国科学技术大学
[source.ustc]
registry = "git://mirrors.ustc.edu.cn/crates.io-index"

# 上海交通大学
[source.sjtu]
registry = "https://mirrors.sjtug.sjtu.edu.cn/git/crates.io-index"

[target.arm-linux-androideabi]
linker = "/home/huangjian/local/toolchains/android_toolchains_r21b/android-ndk-r21b/toolchains/llvm/prebuilt/linux-x86_64/bin/armv7a-linux-androideabi23-clang"

[target.aarch64-linux-android]
linker = "/home/huangjian/local/toolchains/android_toolchains_r21b/android-ndk-r21b/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android29-clang"

[target.armv7-unknown-linux-gnueabihf]
linker = "arm-linux-gnueabihf-gcc-5"
```

