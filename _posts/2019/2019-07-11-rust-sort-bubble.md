---
layout: post
title:  "[Rust] bubble sort"
date:   2019-07-11
comments: true
categories: Rust
tags: [Rust, sort, bubble sort]
description:
published: true
---

```rust
fn main() {
    let mut v = vec![3, 2, 5, 1, 4];
    println!("before sort, v = {:?}", v);

    bubble_sort(&mut v);
    println!("after sort, v = {:?}", v);
}

fn bubble_sort<T>(v: &mut [T])
where
    T: PartialOrd + Copy,
{
    let mut i = 0;
    while i < v.len() {
        let mut j = v.len() - 1;
        while j > i {
            if v[j - 1] > v[j] {
                v.swap(j - 1, j);
            }
            j -= 1;
        }
        i += 1;
    }
}
```
