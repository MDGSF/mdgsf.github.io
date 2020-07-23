---
layout: post
title:  "arm arm32 armv7 arm64 aarch64 armv8 x86 i386 i686 x86_64 amd64"
date:   2020-07-23
comments: true
categories: miscellanea
tags: [cpu]
description:
published: true
---

1. **Intel**. 我们一般买的电脑都是 Intel 的 CPU。
2. **AMD**. AMD 是 Intel 的最大竞争对手。AMD 的 CPU 一般也用在电脑上。
3. **ARM**. ARM 的芯片一般用在手机、平板上。我的手机就是高通骁龙的。

ARM 有两层含义：

1. **ARM**. 是一家半导体芯片设计研发的企业。
2. **ARM**. 是与 x86 平级的 CPU 架构。

**ARM** CPU 架构与 **x86** CPU 架构的区别：

1. **ARM** CPU 架构使用 RISC（精简指令集）。
2. **x86** CPU 架构使用 CISC（复杂指令集）。

每个 CPU 都会实现一套指令集，也叫做 **Instruction Set Architecture**，简称 **ISA**。

标题上的这些名字表示的是不同平台的 CPU 架构。

1. **arm**，**arm32**，**armv7** 是 32 位 ARMv7 架构。
2. **arm64**，**aarch64** 是 64 位 ARMv8 架构。
3. **x86**，**i386**，**i686** 是 32 位 Intel x86 架构。
4. **x86_64**，**amd64** 是 64 位 Intel x86 架构。

现在的手机和电脑一般都是 64 位的，所以如果要给手机编译一个软件，一般使用 aarch64 类型的交叉编译工具链。

## 交叉编译工具链

Triple 三元组格式：

```
{arch}-{vendor}-{sys}-{abi}
```

明明是 4 个项，不知道为什么叫三元组。

例子：

```
arm-unknown-linux-gnueabihf
```

- 架构（architecture）：arm。
- 供应商（vendor）：unknown。这里 unknown 就是未指定供应商或者是不重要。
- 系统（system）：linux。
- ABI：gnueabihf。gnueabihf 表示系统使用 glibc 作为其 C 标准库 libc 实现，
    并且具有硬件加速浮点算法。

然后有的 triple 三元组会把**供应商（vendor）**或**ABI**给省略掉。例如：

```
x86_64-apple-darwin
```

- 架构（architecture）：x86_64。
- 供应商（vendor）：apple。
- 系统（system）：darwin。

下面是一些常见的例子：

```
$ rustc --print target-list | pr -tw100 --columns 3
aarch64-fuchsia                  i686-pc-windows-msvc             sparc-unknown-linux-gnu
aarch64-linux-android            i686-unknown-cloudabi            sparc64-unknown-linux-gnu
aarch64-pc-windows-msvc          i686-unknown-freebsd             sparc64-unknown-netbsd
aarch64-unknown-cloudabi         i686-unknown-haiku               sparc64-unknown-openbsd
aarch64-unknown-freebsd          i686-unknown-linux-gnu           sparcv9-sun-solaris
aarch64-unknown-hermit           i686-unknown-linux-musl          thumbv6m-none-eabi
aarch64-unknown-linux-gnu        i686-unknown-netbsd              thumbv7a-pc-windows-msvc
aarch64-unknown-linux-musl       i686-unknown-openbsd             thumbv7em-none-eabi
aarch64-unknown-netbsd           i686-unknown-uefi                thumbv7em-none-eabihf
aarch64-unknown-none             i686-uwp-windows-gnu             thumbv7m-none-eabi
aarch64-unknown-none-softfloat   i686-uwp-windows-msvc            thumbv7neon-linux-androideabi
aarch64-unknown-openbsd          i686-wrs-vxworks                 thumbv7neon-unknown-linux-gnueab
aarch64-unknown-redox            mips-unknown-linux-gnu           thumbv7neon-unknown-linux-muslea
aarch64-uwp-windows-msvc         mips-unknown-linux-musl          thumbv8m.base-none-eabi
aarch64-wrs-vxworks              mips-unknown-linux-uclibc        thumbv8m.main-none-eabi
arm-linux-androideabi            mips64-unknown-linux-gnuabi64    thumbv8m.main-none-eabihf
arm-unknown-linux-gnueabi        mips64-unknown-linux-muslabi64   wasm32-unknown-emscripten
arm-unknown-linux-gnueabihf      mips64el-unknown-linux-gnuabi64  wasm32-unknown-unknown
arm-unknown-linux-musleabi       mips64el-unknown-linux-muslabi64 wasm32-wasi
arm-unknown-linux-musleabihf     mipsel-unknown-linux-gnu         x86_64-apple-darwin
armebv7r-none-eabi               mipsel-unknown-linux-musl        x86_64-fortanix-unknown-sgx
armebv7r-none-eabihf             mipsel-unknown-linux-uclibc      x86_64-fuchsia
armv4t-unknown-linux-gnueabi     mipsisa32r6-unknown-linux-gnu    x86_64-linux-android
armv5te-unknown-linux-gnueabi    mipsisa32r6el-unknown-linux-gnu  x86_64-linux-kernel
armv5te-unknown-linux-musleabi   mipsisa64r6-unknown-linux-gnuabi x86_64-pc-solaris
armv6-unknown-freebsd            mipsisa64r6el-unknown-linux-gnua x86_64-pc-windows-gnu
armv6-unknown-netbsd-eabihf      msp430-none-elf                  x86_64-pc-windows-msvc
armv7-linux-androideabi          nvptx64-nvidia-cuda              x86_64-rumprun-netbsd
armv7-unknown-cloudabi-eabihf    powerpc-unknown-linux-gnu        x86_64-sun-solaris
armv7-unknown-freebsd            powerpc-unknown-linux-gnuspe     x86_64-unknown-cloudabi
armv7-unknown-linux-gnueabi      powerpc-unknown-linux-musl       x86_64-unknown-dragonfly
armv7-unknown-linux-gnueabihf    powerpc-unknown-netbsd           x86_64-unknown-freebsd
armv7-unknown-linux-musleabi     powerpc-wrs-vxworks              x86_64-unknown-haiku
armv7-unknown-linux-musleabihf   powerpc-wrs-vxworks-spe          x86_64-unknown-hermit
armv7-unknown-netbsd-eabihf      powerpc64-unknown-freebsd        x86_64-unknown-hermit-kernel
armv7-wrs-vxworks-eabihf         powerpc64-unknown-linux-gnu      x86_64-unknown-illumos
armv7a-none-eabi                 powerpc64-unknown-linux-musl     x86_64-unknown-l4re-uclibc
armv7a-none-eabihf               powerpc64-wrs-vxworks            x86_64-unknown-linux-gnu
armv7r-none-eabi                 powerpc64le-unknown-linux-gnu    x86_64-unknown-linux-gnux32
armv7r-none-eabihf               powerpc64le-unknown-linux-musl   x86_64-unknown-linux-musl
asmjs-unknown-emscripten         riscv32i-unknown-none-elf        x86_64-unknown-netbsd
hexagon-unknown-linux-musl       riscv32imac-unknown-none-elf     x86_64-unknown-openbsd
i586-pc-windows-msvc             riscv32imc-unknown-none-elf      x86_64-unknown-redox
i586-unknown-linux-gnu           riscv64gc-unknown-linux-gnu      x86_64-unknown-uefi
i586-unknown-linux-musl          riscv64gc-unknown-none-elf       x86_64-uwp-windows-gnu
i686-apple-darwin                riscv64imac-unknown-none-elf     x86_64-uwp-windows-msvc
i686-linux-android               s390x-unknown-linux-gnu          x86_64-wrs-vxworks
i686-pc-windows-gnu
```

## 资源链接

https://github.com/japaric/rust-cross
