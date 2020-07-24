---
layout: post
title:  "如何选取 android 系统上的编译工具链"
date:   2020-07-23
comments: true
categories: miscellanea
tags: [cpu, arm, arm32, armv7, arm64, aarch64, armv8, x86, i386, i686, x86_64, amd64]
description:
published: true
---

先介绍一些基础背景知识。

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

## 交叉编译工具链三元组格式介绍

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

然后有的 triple 三元组会把**供应商（vendor）**或 **ABI** 给省略掉。例如：

```
x86_64-apple-darwin
```

- 架构（architecture）：x86_64。
- 供应商（vendor）：apple。
- 系统（system）：darwin。

那我们要怎么确定我们要用哪一个呢？

- 架构（architecture）：在 UNIX 系统上，我们可以使用 `uname -m` 查看。
- 供应商（vendor）：Linux 通常是 `unknown`，Windows 是 `pc`，OSX/IOS 是 `apple`。
- 系统（system）：在 UNIX 系统上，我们可以使用 `uname -s` 查看。
- ABI：Linux 上可以使用 `ldd --version` 查看。Mac 和 *BSD 忽略这个选项。Windows 是 `gnu` 或 `msvc`。

因为我们是要给 64 位的 android 系统使用，所以一定要有 **aarch64** 和 **android** 出现，这样就可以排除掉其他不可以使用的了。
下面我们看具体要怎么选。

## android 编译工具链

你可以从 [ndk 下载] 地址下载最新的 ndk。目前最新的稳定版本是 r21b。
我这里是 ubuntu 系统，所以下载 `android-ndk-r21b-linux-x86_64.zip` 这个压缩包。
解压之后得到 `android-ndk-r21b` 这个目录，比如就解压到 `HOME` 目录 `/home/huangjian` 下面。

我们可以编写一个如下的编译脚本，来编译最简单的 hello world 的 cpp 程序。

```sh
export NDK=/home/huangjian/android-ndk-r21b
export TOOLCHAIN=$NDK/toolchains/llvm/prebuilt/linux-x86_64
export TARGET=aarch64-linux-android
export API=29

export AR=$TOOLCHAIN/bin/$TARGET-ar
export AS=$TOOLCHAIN/bin/$TARGET-as
export CC=$TOOLCHAIN/bin/$TARGET$API-clang
export CXX=$TOOLCHAIN/bin/$TARGET$API-clang++
export LD=$TOOLCHAIN/bin/$TARGET-ld
export RANLIB=$TOOLCHAIN/bin/$TARGET-ranlib
export STRIP=$TOOLCHAIN/bin/$TARGET-strip

echo 'AR = '$AR
echo 'AS = '$AS
echo 'CC = '$CC
echo 'CXX = '$CXX
echo 'LD = '$LD
echo 'RANLIB = '$RANLIB
echo 'STRIP = '$STRIP

$CC hello.cpp -o hello
```

我们可以看到在目录 `/home/huangjian/android-ndk-r21b/toolchains/llvm/prebuilt/linux-x86_64/bin`
下面，有非常多的不同 CPU 架构的编译器，我们需要选择其中一个来使用。上面的编译脚本中，
我们选择的是 `aarch64-linux-android29-clang`。

```
aarch64-linux-android21-clang       armv7a-linux-androideabi23-clang    ld.lld
aarch64-linux-android21-clang++     armv7a-linux-androideabi23-clang++  lldb-argdumper
aarch64-linux-android22-clang       armv7a-linux-androideabi24-clang    llvm-addr2line
aarch64-linux-android22-clang++     armv7a-linux-androideabi24-clang++  llvm-ar
aarch64-linux-android23-clang       armv7a-linux-androideabi26-clang    llvm-as
aarch64-linux-android23-clang++     armv7a-linux-androideabi26-clang++  llvm-cfi-verify
aarch64-linux-android24-clang       armv7a-linux-androideabi27-clang    llvm-config
aarch64-linux-android24-clang++     armv7a-linux-androideabi27-clang++  llvm-cov
aarch64-linux-android26-clang       armv7a-linux-androideabi28-clang    llvm-dis
aarch64-linux-android26-clang++     armv7a-linux-androideabi28-clang++  llvm-lib
aarch64-linux-android27-clang       armv7a-linux-androideabi29-clang    llvm-link
aarch64-linux-android27-clang++     armv7a-linux-androideabi29-clang++  llvm-modextract
aarch64-linux-android28-clang       bisect_driver.py                    llvm-nm
aarch64-linux-android28-clang++     clang                               llvm-objcopy
aarch64-linux-android29-clang       clang++                             llvm-objdump
aarch64-linux-android29-clang++     clang-check                         llvm-profdata
aarch64-linux-android-addr2line     clang-cl                            llvm-ranlib
aarch64-linux-android-ar            clang-format                        llvm-readelf
aarch64-linux-android-as            clang-tidy                          llvm-readobj
aarch64-linux-android-c++filt       clang-tidy.real                     llvm-size
aarch64-linux-android-dwp           dsymutil                            llvm-strings
aarch64-linux-android-elfedit       git-clang-format                    llvm-strip
aarch64-linux-android-gprof         i686-linux-android16-clang          llvm-symbolizer
aarch64-linux-android-ld            i686-linux-android16-clang++        sancov
aarch64-linux-android-ld.bfd        i686-linux-android17-clang          sanstats
aarch64-linux-android-ld.gold       i686-linux-android17-clang++        scan-build
aarch64-linux-android-nm            i686-linux-android18-clang          scan-view
aarch64-linux-android-objcopy       i686-linux-android18-clang++        x86_64-linux-android21-clang
aarch64-linux-android-objdump       i686-linux-android19-clang          x86_64-linux-android21-clang++
aarch64-linux-android-ranlib        i686-linux-android19-clang++        x86_64-linux-android22-clang
aarch64-linux-android-readelf       i686-linux-android21-clang          x86_64-linux-android22-clang++
aarch64-linux-android-size          i686-linux-android21-clang++        x86_64-linux-android23-clang
aarch64-linux-android-strings       i686-linux-android22-clang          x86_64-linux-android23-clang++
aarch64-linux-android-strip         i686-linux-android22-clang++        x86_64-linux-android24-clang
arm-linux-androideabi-addr2line     i686-linux-android23-clang          x86_64-linux-android24-clang++
arm-linux-androideabi-ar            i686-linux-android23-clang++        x86_64-linux-android26-clang
arm-linux-androideabi-as            i686-linux-android24-clang          x86_64-linux-android26-clang++
arm-linux-androideabi-c++filt       i686-linux-android24-clang++        x86_64-linux-android27-clang
arm-linux-androideabi-dwp           i686-linux-android26-clang          x86_64-linux-android27-clang++
arm-linux-androideabi-elfedit       i686-linux-android26-clang++        x86_64-linux-android28-clang
arm-linux-androideabi-gprof         i686-linux-android27-clang          x86_64-linux-android28-clang++
arm-linux-androideabi-ld            i686-linux-android27-clang++        x86_64-linux-android29-clang
arm-linux-androideabi-ld.bfd        i686-linux-android28-clang          x86_64-linux-android29-clang++
arm-linux-androideabi-ld.gold       i686-linux-android28-clang++        x86_64-linux-android-addr2line
arm-linux-androideabi-nm            i686-linux-android29-clang          x86_64-linux-android-ar
arm-linux-androideabi-objcopy       i686-linux-android29-clang++        x86_64-linux-android-as
arm-linux-androideabi-objdump       i686-linux-android-addr2line        x86_64-linux-android-c++filt
arm-linux-androideabi-ranlib        i686-linux-android-ar               x86_64-linux-android-dwp
arm-linux-androideabi-readelf       i686-linux-android-as               x86_64-linux-android-elfedit
arm-linux-androideabi-size          i686-linux-android-c++filt          x86_64-linux-android-gprof
arm-linux-androideabi-strings       i686-linux-android-dwp              x86_64-linux-android-ld
arm-linux-androideabi-strip         i686-linux-android-elfedit          x86_64-linux-android-ld.bfd
armv7a-linux-androideabi16-clang    i686-linux-android-gprof            x86_64-linux-android-ld.gold
armv7a-linux-androideabi16-clang++  i686-linux-android-ld               x86_64-linux-android-nm
armv7a-linux-androideabi17-clang    i686-linux-android-ld.bfd           x86_64-linux-android-objcopy
armv7a-linux-androideabi17-clang++  i686-linux-android-ld.gold          x86_64-linux-android-objdump
armv7a-linux-androideabi18-clang    i686-linux-android-nm               x86_64-linux-android-ranlib
armv7a-linux-androideabi18-clang++  i686-linux-android-objcopy          x86_64-linux-android-readelf
armv7a-linux-androideabi19-clang    i686-linux-android-objdump          x86_64-linux-android-size
armv7a-linux-androideabi19-clang++  i686-linux-android-ranlib           x86_64-linux-android-strings
armv7a-linux-androideabi21-clang    i686-linux-android-readelf          x86_64-linux-android-strip
armv7a-linux-androideabi21-clang++  i686-linux-android-size             yasm
armv7a-linux-androideabi22-clang    i686-linux-android-strings
armv7a-linux-androideabi22-clang++  i686-linux-android-strip
```

## Rust 交叉编译工具链选择

下面列出 Rust 支持的交叉编译平台：

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

我们可以直接搜索和 android 相关的：

```
$ rustc --print target-list | grep android
aarch64-linux-android
arm-linux-androideabi
armv7-linux-androideabi
i686-linux-android
thumbv7neon-linux-androideabi
x86_64-linux-android
```

- aarch64 是 64 位的 ARM CPU 架构。
- arm 是 32 位的 ARM CPU 架构。
- armv7 是 32 位的 ARM CPU 架构。
- i686 是 32 位的 x86 CPU 架构。
- x86_64 是 64 位的 x86 CPU 架构。
- thumbv7neon 不认识。

很明显我们就是选择第一个 `aarch64-linux-android`。

然后我们需要安装下这个平台的开发环境：

```
rustup target add aarch64-linux-android
```

安装好之后在 `~/.cargo/config` 文件里面添加如下内容：

```
[target.aarch64-linux-android]
linker = "/home/huangjian/android-ndk-r21b/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android29-clang"
```

这里的 linker 填写的就是我们前面下载的 android 交叉编译工具目录下的编译器路径。

编译命令如下：

```
cargo build --target=aarch64-linux-android
```

## 资源链接

- https://developer.android.com/ndk
- https://developer.android.com/ndk/guides/other_build_systems
- https://github.com/japaric/rust-cross

[ndk 下载]: https://developer.android.com/ndk/downloads
