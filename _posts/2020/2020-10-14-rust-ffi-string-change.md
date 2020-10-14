---
layout: post
title:  "[Rust] ffi String,char*,void*,slice 转换"
date:   2020-10-14
comments: true
categories: Rust
tags: [Rust, ffi, String]
description:
published: true
---

## String 转换为 const char *

```rust
use std::ffi::CString;
use std::os::raw::c_char;
fn main() {
  let hello = String::from("hello");
  let chello = CString::new(hello).unwrap();
  let chello = chello.as_bytes_with_nul().as_ptr() as *const c_char;
}
```

## String 转换为 const void *

```rust
use std::ffi::CString;
use std::os::raw::c_void;
fn main() {
  let hello = String::from("hello");
  let chello = CString::new(hello).unwrap();
  let chello = chello.as_bytes_with_nul().as_ptr() as *const c_void;
}
```

## 静态数组转换为 const char *

```rust
use std::ffi::CStr;
use std::os::raw::c_char;

pub const HELLO: &'static [u8; 6usize] = b"hello\0";

fn main() {
  let hello = CStr::from_bytes_with_nul(HELLO).unwrap();
  let hello = hello.as_ptr() as *const c_char;
}
```

## const char * 转换为 String

```rust
use std::ffi::CStr;
use std::os::raw::c_char;

fn c_to_string(hello: *const c_char) -> String {
  let hello = unsafe { CStr::from_ptr(hello).to_str().unwrap() };
  let hello = String::from(hello);
  hello
}

fn main() {}
```

## buffer 转换为 slice

```rust
use std::os::raw::c_void;
use std::slice;

fn buf_to_slice(buf: *const c_void, len: usize) {
  let data = unsafe { slice::from_raw_parts(buf as *const u8, len) };
  // let _: () = data; // &[u8]
}

fn main() {}
```

