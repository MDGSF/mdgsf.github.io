---
layout: post
title:  "[Rust] array, vector and slice"
date:   2019-07-11
comments: true
categories: Rust
tags: [Rust, array, vector, slice]
description:
published: true
---

## Example 1

```rust
fn test1(mut a: [i32; 3]) {
    a[0] = 10;
    println!("test1 a = {:?}", a); // test1 a = [10, 2, 3]
}

fn test2(mut b: Vec<i32>) {
    b[0] = 20;
    println!("test2 b = {:?}", b); // test2 b = [20, 2, 3]
}

fn test3(c: &mut [i32]) {
    c[0] = 30;
    println!("test3 c = {:?}", c); // test3 c = [30, 2, 3]
}

fn main() {
    // a 默认实现 Copy trait，所以在传入 test1 函数时执行了按位拷贝整个数组。
    let a = [1, 2, 3];
    test1(a);
    println!("main a = {:?}", a); // main a = [1, 2, 3]

    // b 的所有权转移到了函数 test2 中去了。
    let b = vec![1, 2, 3];
    test2(b);
    // println!("main b = {:?}", b);

    // 推荐这么使用，在拥有所有权的地方使用 vector 动态数组，
    // 在租借出去的时候，使用 mutable slice 来接收。
    let mut c = vec![1, 2, 3];
    test3(&mut c);
    println!("main c = {:?}", c); // main c = [30, 2, 3]
}
```

## Example 2

```rust
fn main() {
    /*
    array, a fixed-size list of elements of the type.
    By default, arrays are immutable.
    [T; N], T is array's element type, and N is the length.
    */
    println!("array demo:");
    let a = [1, 2, 3]; // a: [i32, 3]
    let b: [i32; 3] = [1, 2, 3]; // b: [i32; 3]
    assert_eq!(a, b);

    /*
    create a mutable array c.
    */
    let mut c = [1, 2, 3]; // mut c: [i32; 3]
    c[0] = 4;
    println!("c = {:?}", c);

    /*
    d.len() = 20, and every element of d will be initialized to 0.
    */
    let d = [0; 20]; // d: [i32; 20]
    println!("d = {:?}", d);

    let e = [1, 2, 3];
    println!("e has {} elements", e.len());
    for t in e.iter() {
        print!("{} ", t);
    }
    println!();

    let names = ["Graydon", "Brian", "Niko"]; // names: [&str; 3]
    println!("The second name is: {}", names[1]);

    /*
    vector, is a dynamic and "growable" array. Vectors always allocate
    the data on the heap.
    Vec<T>, T is vector's element type.
    */
    println!("\nvector demo:");
    let v = vec![1, 2, 3]; // v: Vec<i32>
    println!("v = {:?}", v);

    let mut nums = vec![1, 2, 3]; // mut nums: Vec<i32>
    nums.push(4);
    println!("The length of nums is now {}", nums.len());

    /*
    slice, is a reference to an array.
    You can also take a slice of a vector, String or &str, because
    they are backed by arrays.
    &[T], T is slice's element type.
    */
    println!("\nslice demo:");
    let a = [0, 1, 2, 3, 4];
    let middle = &a[1..4]; // middle is a slice of a.
    print!("slice of array = ");
    for e in middle.iter() {
        print!("{} ", e);
    }
    println!();

    let a = vec![0, 1, 2, 3, 4];
    let middle = &a[1..4]; // middle is a slice of a.
    print!("slice of vector = ");
    for e in middle.iter() {
        print!("{} ", e);
    }
    println!();

    let a = String::from("01234");
    let middle = &a[1..4]; // middle is a slice of a.
    println!("slice of String = {}", middle);

    let mut a = [0, 1, 2, 3, 4];
    let middle = &mut a[1..4]; // middle is a mutable slice of a.
    print!("mutable slice of array = ");
    for e in middle.iter_mut() {
        *e += 10;
        print!("{} ", e);
    }
    println!();

    println!("\nothers:");
    let x: &mut [i32; 3] = &mut [1, 2, 4]; // &mut [i32; 3] 这个类型应该是对数组 [i32; 3] 的可变引用。
    x.len();
    x[0] = 10;
    println!("x = {:?}", x);

    test1();
}

fn test1() {
    // a is an array
    let mut a = [1, 2, 3];
    test(&mut a);

    // b is an vector
    let mut b = vec![4, 5, 6, 7, 8, 9];
    test(&mut b);

    // c and d is a slice
    let c = &mut a[1..];
    let d = &mut c[1..];
    test(d);
}

// test function use a mutable slice as parameter.
fn test(a: &mut [i32]) {
    for e in a.iter_mut() {
        *e += 10
    }
    println!("slice = {:?}", a);
}
```

[https://www.cs.brandeis.edu/~cs146a/rust/doc-02-21-2015/book/arrays-vectors-and-slices.html](https://www.cs.brandeis.edu/~cs146a/rust/doc-02-21-2015/book/arrays-vectors-and-slices.html)
