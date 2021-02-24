---
layout: post
title: "[Rust] 生命周期"
date: 2021-02-20
comments: true
categories: Rust
tags: [Rust, lifetime]
description:
published: true
---

- [生命周期介绍](#生命周期介绍)
- [生命周期自动推断](#生命周期自动推断)
- [longest 生命周期例子](#longest-生命周期例子)
  - [longest 例子一](#longest-例子一)
  - [longest 例子二](#longest-例子二)
  - [longest 例子三](#longest-例子三)
  - [longest 例子四](#longest-例子四)
  - [longest 例子五](#longest-例子五)
  - [longest 例子六](#longest-例子六)
- [结构体生命周期标注](#结构体生命周期标注)
  - [结构体例子一](#结构体例子一)
  - [结构体例子二](#结构体例子二)
  - [结构体例子三](#结构体例子三)
- [生命周期限定](#生命周期限定)
  - [例子一](#例子一)
  - [例子二](#例子二)
- [静态生命周期标注](#静态生命周期标注)
- [early bound and late bound](#early-bound-and-late-bound)
  - [例子一 -- Rust-Quiz 11](#例子一----rust-quiz-11)
  - [例子二 -- late bound](#例子二----late-bound)
  - [例子三 -- early bound](#例子三----early-bound)
- [生命周期T和&amp;T](#生命周期t和t)
  - [例子一](#例子一-1)
  - [例子二 -- Rust-Quiz 5](#例子二----rust-quiz-5)
- [Trait Object 生命周期](#trait-object-生命周期)
  - [Trait Object 默认生命周期](#trait-object-默认生命周期)
  - [Trait Object 例子一](#trait-object-例子一)
  - [Trait Object 例子二](#trait-object-例子二)
  - [Trait Object 例子三](#trait-object-例子三)
  - [Trait Object 例子四](#trait-object-例子四)
- [HRTB -- Higher Ranked Trait Bound](#hrtb----higher-ranked-trait-bound)
  - [HRTB例子一](#hrtb例子一)
  - [HRTB例子二](#hrtb例子二)
  - [HRTB例子三](#hrtb例子三)
- [生命周期其他例子](#生命周期其他例子)
  - [例子一](#例子一-2)
  - [例子二](#例子二-1)
- [相关资源](#相关资源)

## 生命周期介绍

**生命周期的意义：生命周期是为了防止悬垂指针。**

什么时候我们会需要用到生命周期参数标注？每个变量都有属于自己的生命周期。但是只有
当我们用到了引用，才需要考虑生命周期参数标注。每个引用都有一个生命周期参数。

A 拥有数据的所有权，那么 A 就是出借方。B 从 A 那里借用了数据，那么 B 就是借用方。
那么 A 的生命周期一定要比 B 的生命周期长。也就是说 A 要活得比 B 更长。也就是所有
权出借方的生命周期要比所有权借用方的生命周期长。为什么呢？假设 A 的生命周期比 B
的更短，那么 B 就会出现悬垂引用。

Rust 的编译器中有一个叫做生命周期检查器的工具，英文名叫做 borrow checker。在大部
分时候，它都可以自动推断出引用的生命周期参数，比如在一个函数内部。
不过在一些特殊的情况下，生命周期存在不确定的情况，这个时候就需要我们自己手动标注生命周期参数，
来告诉它我们希望用哪种情况，比如出现了跨函数的引用。

引用如果只是在一个函数体的内部使用，那么 borrow checker 是可以自己推断出生命周期
的。只有当引用用在了函数的参数、返回值的时候，borrow checker 就做不到自动推断了
，这个时候就需要手动标注生命周期参数。

我们手动标注的生命周期参数并不会改变引用的生命周期，它只是用来帮助
borrow checker 检查我们的代码。

```rust
// 'a 是出借方
// 'b 是借用方
// 'a 的生命周期 >= 'b 的生命周期
// 'a 是 'b 的子类型，和类继承类似
fn foo<'a: 'b>() {
}
```

返回值引用一定和某个参数引用有关系，否则的话返回值引用的就是函数体内的局部
变量，函数结束之后就会有悬垂指针，是不允许的。例如下面的例子就无法通过编译：

```rust
fn return_str<'a>() -> &'a str {
  let mut s = "Rust".to_string();
  for _i in 0..3 {
    s.push_str("Good ");
  }
  &s[..] // returns a value referencing data owned by the current function
}

fn main() {
  let x = return_str();
}
```

再看一个例子，下面这里例子中，`foo` 函数的参数和返回值都标注了 `'a` 的生命周期，
表示返回值的生命周期至少和 `x 与 y` 两个生命周期中的较小者一样长，也就是
`x >= 返回值 && y >= 返回值`。但是实际上返回值和参数并没有任何关系，也是通不过编
译的。

```rust
fn foo<'a>(x: &'a str, y: &'a str) -> &'a str {
  let result = String::from("really long string");
  result.as_str() // returns a value referencing data owned by the current function
}

fn main() {
  let x = "hello";
  let y = "rust";
  foo(x, y);
}
```

## 生命周期自动推断

满足以下 3 条规则可以自动推断生命周期：

1. 函数的每个引用参数都有一个独立的生命周期标注。

  ```rust
  fn foo<'a>(x: &'a i32);
  fn foo<'a, 'b>(x: &'a i32, y: &'b i32);
  fn foo<'a, 'b, 'c>(x: &'a i32, y: &'b i32, z: &'c i32);
  ```

2. 如果刚好只有一个引用参数，那这个引用参数的生命周期标注直接
  应用在所有返回值的引用上。

  ```rust
  fn foo<'a>(x: &'a i32) -> &'a i32
  ```

3. 如果方法的第一个参数是 `&self` 或 `&mut self`，那么直接把这个参数的
  生命周期标注应用在返回值的引用上。

  ```rust
  // 应用 1 和 2 规则：
  fn first_word(s: &str) -> &str
  fn first_word<'a>(s: &'a str) -> &'a str
  ```

  ```rust
  // 应用 1 规则：
  fn longest<'a, 'b>(x: &'a str, y: &'b str) -> &str
  // 可以看到 2 和 3 规则推断不出来返回值的生命周期标注，所以只好手动标注。
  ```

## longest 生命周期例子

### longest 例子一

```rust
/*
下面函数等价于：
fn longest<'a, 'b, 'c>(x: &'a str, y: &'b str) -> &'c str
参数 x，参数 y 和返回值都是引用，所以他们都有一个自己的生命周期参数。
但是生命周期检查器并不知道这 3 个生命周期参数之间有什么关系。
*/
fn longest(x: &str, y: &str) -> &str {
  if x.len() > y.len() {
    x
  } else {
    y
  }
}

fn main() {
  let string1 = String::from("abcd");
  let string2 = "xyz";

  let result = longest(string1.as_str(), string2);
  println!("The longest string is {}", result);
}
```

这个例子是无法通过编译的。报错如下：

```shell
error[E0106]: missing lifetime specifier
  --> src/main.rs:11:33
   |
11 | fn longest(x: &str, y: &str) -> &str {
   |               ----     ----     ^ expected named lifetime parameter
   |
   = help: this function's return type contains a borrowed value, but
           the signature does not say whether it is borrowed from `x` or `y`
help: consider introducing a named lifetime parameter
   |
11 | fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
   |           ^^^^    ^^^^^^^     ^^^^^^^     ^^^
```

### longest 例子二

我们按照上面那个例子中的提示修改代码如下：

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
  if x.len() > y.len() {
    x
  } else {
    y
  }
}
```

这个例子可以通过编译。这个例子中的 `longest` 函数的生命周期参数标注中，参数 x，
参数 y 和返回值使用相同的生命周期标注`'a`。表示返回值的生命周期至少和
`参数 x 的生命周期和参数 y 的生命周期之间的较小者` 一样长。

### longest 例子三

```rust
fn longest<'a: 'c, 'b: 'c, 'c>(x: &'a str, y: &'b str) -> &'c str {
  if x.len() > y.len() {
    x
  } else {
    y
  }
}
```

这个例子可以通过编译。这个例子中，给每个参数和返回值都独立标注了生命周期。
`'a: 'c` 表示参数 x 的生命周期大于等于返回值的生命周期。
`'b: 'c` 表示参数 y 的生命周期大于等于返回值的生命周期。

### longest 例子四

那我们能不能给所有的参数使用一个生命周期标注，返回值单独使用一个生命周期标注呢？

```rust
fn longest<'a: 'c, 'c>(x: &'a str, y: &'a str) -> &'c str {
  if x.len() > y.len() {
    x
  } else {
    y
  }
}
```

这个例子可以通过编译。`'a: 'c` 表示参数 x 的生命周期大于等于返回值的生命周期，
同时参数 y 的生命周期也大于等于返回值的生命周期

### longest 例子五

那如果返回值只和参数 x 有关系，和参数 y 没有关系，又要怎么标注？

```rust
fn longest<'a>(x: &'a str, y: &str) -> &'a str {
  x
}
```

上面的函数等价于：

```rust
fn longest<'a, 'b>(x: &'a str, y: &'b str) -> &'a str {
  x
}
```

这个例子可以通过编译。可以看到返回值的生命周期参数只和参数 x 有关系。参数 x
的生命周期参数大于等于返回值的生命周期参数。

### longest 例子六

```rust
fn longest<'a: 'c, 'b, 'c>(x: &'a str, y: &'b str) -> &'c str {
  x
}
```

这个例子可以通过编译。上面的例子也可以修改为给每个参数和返回值单独标注生命周期参
数，只要指明了返回值的生命周期标注和输入参数之间的关系就可以了。

## 结构体生命周期标注

### 结构体例子一

```rust
struct A<'a> {
  name: &'a str,
}

impl<'a> A<'a> {
  // 等价于 fn get<'b>(&'b self) -> &'b str
  // 为啥这里是 'b 而不是 'a 呢？
  // 'a 是 name 引用的 s 的生命周期
  // 'b 是 A 实例化出来的对象 a 的生命周期
  // 根据生命周期自动推断规则，这个函数的返回值和对象 a 生命周期一样
  fn get(&self) -> &str {
    self.name
  }
}

fn main() {
  let s = String::from("hello");
  let s_ref;
  {
    let a = A { name: &s };
    s_ref = a.get();
  }
  println!("{:?}", s_ref);
}
```

这个例子是无法通过编译的。因为 `s_ref` 的生命周期比 `a.get()` 返回值的生命周期
更长。长生命周期是不能引用短生命周期的。否则就会出现悬垂指针。

```shell
error[E0597]: `a` does not live long enough
  --> src/main.rs:22:13
   |
22 |     s_ref = a.get();
   |             ^ borrowed value does not live long enough
23 |   }
   |   - `a` dropped here while still borrowed
24 |
25 |   println!("{:?}", s_ref);
   |                    ----- borrow later used here
```

我们之需要稍作修改，就可以通过编译：

```rust
impl<'a> A<'a> {
  // 等价于 fn get<'b>(&'b self) -> &'a str
  // 返回值的生命周期和 name 引用的 s 变量一样长
  // 而 'b 则是代表 A 实例化出来的对象的生命周期
  fn get(&self) -> &'a str {
    self.name
  }
}
```

我们把 `a.get()` 的返回值的生命周期不和对象 `a` 绑定在一起，而是和 `s`
绑定在一起。就能够顺利通过编译。

### 结构体例子二

```rust
#[derive(Debug)]
struct ImportantExcerpt<'a> {
  part: &'a str,
}

fn main() {
  let noval = String::from("Call me Ishmael. Some years ago...");
  let first_sentence = noval.split('.').next().expect("Could not find a '.'");
  let i = ImportantExcerpt {
    part: first_sentence,
  };
  println!("i = {:?}", i);
}
```

结构体 `ImportantExcerpt` 中的 `'a` 意味着 `ImportantExcerpt` 实例化出来的对象的
生命周期不能超过成员 `part` 所引用的对象的生命周期。在 main 函数中的 `i` 的生命
周期不能超过 `noval` 的生命周期。

### 结构体例子三

结构体成员变量用到的生命周期标注需要在 impl 和结构体名字后面加上。
`impl<'a>` 是声明 `'a`，而 `ImportantExcerpt<'a>` 是使用 `'a`。

```rust
#[derive(Debug)]
struct ImportantExcerpt<'a> {
  part: &'a str,
}

impl<'a> ImportantExcerpt<'a> {
  fn level(&self) -> i32 {
    3
  }

  fn nrp(&self, announcement: &str) -> &str {
    println!("Attention please: {}", announcement);
    self.part
  }
}

fn main() {
  let noval = String::from("Call me Ishmael. Some years ago...");
  let first_sentence = noval.split('.').next().expect("Could not find a '.'");
  let i = ImportantExcerpt {
    part: first_sentence,
  };
  println!("i = {:?}", i);
}
```

结构体的例子比较复杂，可以对上面的例子进行如下扩展：

**例子2.1**：可以通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn level(&self) -> i32 {
    3
  }
}
```

**例子2.2**：可以通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn level(&'a self) -> i32 {
    3
  }
}
```

**例子2.3**：无法通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn level<'a>(&'a self) -> i32 {
    3
  }
}
```

```shell
error[E0496]: lifetime name `'a` shadows a lifetime name that is already in scope
  --> src/main.rs:18:12
   |
8  | impl<'a> ImportantExcerpt<'a> {
   |      -- first declared here
...
18 |   fn level<'a>(&'a self) -> i32 {
   |            ^^ lifetime 'a already in scope
```

根据报错来看，应该是 `impl<'a>` 声明了 `'a`，然后 `level<'a>` 又重复声明了
`'a`，在同一个作用域里声明了两次，所以报错了。

**例子2.4**：无法通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn level(&'b self) -> i32 {
    3
  }
}
```

```shell
error[E0261]: use of undeclared lifetime name `'b`
  --> src/main.rs:18:13
   |
18 |   fn level(&'b self) -> i32 {
   |             ^^ undeclared lifetime
   |
help: consider introducing lifetime `'b` here
   |
8  | impl<'b, 'a> ImportantExcerpt<'a> {
   |      ^^^
help: consider introducing lifetime `'b` here
   |
18 |   fn level<'b>(&'b self) -> i32 {
   |           ^^^^
```

根据报错，是说 `'b` 没有声明，提示说可以在 `impl` 那里加上，也可以在函数 `level`
那里加上，不过没有说在 `impl` 那里和在函数 `level` 那里加上有什么区别。

**例子2.5**：可以通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn level<'b>(&'b self) -> i32 {
    3
  }
}
```

**例子2.6**：可以通过编译

```rust
impl<'a, 'b> ImportantExcerpt<'a> {
  fn level(&'b self) -> i32 {
    3
  }
}
```

**例子2.7**：可以通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn nrp(&self, announcement: &str) -> &str {
    println!("Attention please: {}", announcement);
    self.part
  }
}
```

**例子2.8**：可以通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn nrp<'b>(&'a self, announcement: &'b str) -> &'a str {
    println!("Attention please: {}", announcement);
    self.part
  }
}
```

**例子2.9**：可以通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn nrp(&'a self, announcement: &'a str) -> &'a str {
    println!("Attention please: {}", announcement);
    self.part
  }
}
```

**例子2.10**：无法通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn nrp<'b>(&'a self, announcement: &'b str) -> &'b str {
    println!("Attention please: {}", announcement);
    self.part
  }
}
```

```shell
error[E0312]: lifetime of reference outlives lifetime of borrowed content...
  --> src/main.rs:34:5
   |
34 |     self.part
   |     ^^^^^^^^^
   |
note: ...the reference is valid for the lifetime `'b` as defined on the method body at 32:10...
  --> src/main.rs:32:10
   |
32 |   fn nrp<'b>(&'a self, announcement: &'b str) -> &'b str {
   |          ^^
note: ...but the borrowed content is only valid for the lifetime `'a` as defined on the impl at 8:6
  --> src/main.rs:8:6
   |
8  | impl<'a> ImportantExcerpt<'a> {
   |      ^^
```

`self.part` 的生命周期是 `'a`，而返回值的生命周期标注是 `'b`，`'a` 和 `'b`
并没有任何关系。

**例子2.11**：可以通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn nrp<'b>(&'b self, announcement: &'b str) -> &'b str {
    println!("Attention please: {}", announcement);
    self.part
  }
}
```

**例子2.12**：可以通过编译

```rust
#[derive(Debug)]
struct ImportantExcerpt<'a> {
  part: &'a str,
}

impl<'b> ImportantExcerpt<'b> {
  fn nrp(&'b self, announcement: &'b str) -> &'b str {
    println!("Attention please: {}", announcement);
    self.part
  }
}
```

那我干脆全部改为 `'b` 好了，但是定义结构体的时候用的是 `'a`，
这样也可以通过编译。

**例子2.13**：可以通过编译

```rust
impl<'a> ImportantExcerpt<'a> {
  fn nrp<'b, 'c>(&'b self, announcement: &'c str) -> &'b str {
    println!("Attention please: {}", announcement);
    self.part
  }
}
```

参数 announcement 和返回值没有任何关系，所以生命周期参数随便标都可以。

**例子2.14**：可以通过编译

```rust
impl<'a, 'b> ImportantExcerpt<'a> {
  fn nrp(&'a self, announcement: &'b str) -> &'a str {
    println!("Attention please: {}", announcement);
    self.part
  }
}
```

## 生命周期限定

### 例子一

```rust
fn foo<'a, 'b>(x: &'a i32, mut y: &'b i32)
where
  'a: 'b,
{
  // &'a i32 is a subtype of &'b i32 because 'a: 'b
  y = x;

  let r: &'b &'a i32 = &&0;
}
```

这个例子可以通过编译。因为 `'a: 'b`，所以 `'a` 的生命周期大于 `'b` 的生命周期。
短生命周期的可以引用长生命周期的。反过来就不行了。

### 例子二

```rust
fn foo<'a, 'b>(x: &'a i32, mut y: &'b i32)
where
  'a: 'b,
{
  let r: &'a &'b i32 = &&0;
}
```

```shell
error[E0491]: in type `&'a &'b i32`, reference has a longer lifetime than the data it references
  --> src/main.rs:12:10
   |
12 |   let r: &'a &'b i32 = &&0;
   |          ^^^^^^^^^^^
```

## 静态生命周期标注

因为 `'a: 'b` 表示 `'a` 的生命周期大于等于 `'b` 的生命周期，那么所有的生命周期标
注都满足 `'static: 'x`，`'static` 表示静态生命周期，和整个程序的生命周期一样长。

```rust
let s: &'static str = "I have a static lifetime.";
```

但是要注意：

```rust
T: 'static
```

上面代码并不是表示 T 的生命周期和整个程序的生命周期一样长。而是说 T 只包含拥有所
有权的数据，或者是包含生命周期为 `'static` 的数据。T 不能包含有短生命周期的引用。

## early bound and late bound

### 例子一 -- Rust-Quiz 11

https://dtolnay.github.io/rust-quiz/11

```rust
fn f<'a>() {}
fn g<'a: 'a>() {}

fn main() {
  let pf = f::<'static> as fn(); // late bound
  let pg = g::<'static> as fn(); // early bound

  println!("{}", pf == pg);
}
```

无法通过编译：报错如下：

```shell
error: cannot specify lifetime arguments explicitly if late bound lifetime parameters are present
  --> src/main.rs:18:16
   |
18 |   let pf = f::<'static> as fn(); // late bound
   |                ^^^^^^^
   |
note: the late bound lifetime parameter is introduced here
  --> src/main.rs:14:6
   |
14 | fn f<'a>() {}
   |      ^^
```

根据报错，是说 `fn f<'a>() {}` 是 `late bound`，所以没法直接指定为 `'static`。

- `early bound` 应该就是编译时就能够确定的。
- `late bound` 应该就是要到程序运行的时候才能够确定。


### 例子二 -- late bound

```rust
struct Buffer {
  buf: Vec<u8>,
  pos: usize,
}

impl Buffer {
  fn new() -> Buffer {
    Buffer {
      buf: vec![1, 2, 3, 4, 5, 6],
      pos: 0,
    }
  }

  fn read_bytes<'a>(&'a mut self) -> &'a [u8] {
    self.pos += 3;
    &self.buf[self.pos - 3..self.pos]
  }
}

fn print(b1: &[u8], b2: &[u8]) {
  println!("{:#?}, {:#?}", b1, b2);
}

fn main() {
  let mut buf = Buffer::new();
  let b1 = buf.read_bytes();
  //let b1 = &(buf.read_bytes().to_owned());
  let b2 = buf.read_bytes();
  print(b1, b2);
}
```

报错如下，说是有两个可变借用。

```shell
error[E0499]: cannot borrow `buf` as mutable more than once at a time
  --> src/main.rs:28:12
   |
26 |   let b1 = buf.read_bytes();
   |            --- first mutable borrow occurs here
27 |   //let b1 = &(buf.read_bytes().to_owned());
28 |   let b2 = buf.read_bytes();
   |            ^^^ second mutable borrow occurs here
29 |   print(b1, b2);
   |         -- first borrow later used here
```

### 例子三 -- early bound

这个例子可以通过编译。

```rust
struct Buffer<'a> {
  buf: &'a [u8],
  pos: usize,
}

// 'a 可以认为是 main 函数中 v 的生命周期
// 'b 可以认为是 main 函数中 buf 这个对象的生命周期
// 'a 的意思是说 Buffer 这个类 new 出来的对象的生命周期不能超过 'a
impl<'a: 'b, 'b> Buffer<'a> {
  fn new(b: &'a [u8]) -> Buffer {
    Buffer { buf: b, pos: 0 }
  }

  fn read_bytes(&'b mut self) -> &'a [u8] {
    self.pos += 3;
    &self.buf[self.pos - 3..self.pos]
  }
}

fn print(b1: &[u8], b2: &[u8]) {
  println!("{:#?}, {:#?}", b1, b2);
}

fn main() {
  let v = vec![1, 2, 3, 4, 5, 6];
  let mut buf = Buffer::new(&v);
  let b1 = buf.read_bytes();
  let b2 = buf.read_bytes();

  // 即使把 buf 删除了，b1 和 b2 也不受影响
  drop(buf);

  print(b1, b2);
}
```

## 生命周期T和&amp;T

### 例子一

这个例子可以通过编译。

```rust
use std::fmt::Debug;

#[derive(Debug)]
struct Ref<'a, T: 'a>(&'a T);

fn print<T>(t: T)
where
  T: Debug,
{
  println!("`print`: t is {:?}", t);
}

fn print_ref<'a, T>(t: &'a T)
where
  T: Debug + 'a,
{
  println!("`print_ref`: t is {:?}", t);
}

fn main() {
  let x = 7;
  let ref_x = Ref(&x);
  print_ref(&ref_x);
  print(ref_x);
}
```

### 例子二 -- Rust-Quiz 5

https://dtolnay.github.io/rust-quiz/5

https://zhuanlan.zhihu.com/p/51616607

该例子可以通过编译。

```rust
trait Trait {
  fn f(self);
}

impl<T> Trait for fn(T) {
  fn f(self) {
    print!("1");
  }
}

impl<T> Trait for fn(&T) {
  fn f(self) {
    print!("2");
  }
}

fn main() {
  let a: fn(_) = |_: u8| {};
  let b: fn(_) = |_: &u8| {};
  let c: fn(&_) = |_: &u8| {};
  a.f(); // 1
  b.f(); // 1
  c.f(); // 2
}
```

## Trait Object 生命周期

Trait 对象生命周期相关文档：
https://doc.rust-lang.org/reference/lifetime-elision.html#default-trait-object-lifetimes

### Trait Object 默认生命周期

Trait 对象默认的生命周期情况：

```rust
// For the following trait...
trait Foo { }

// These two are the same as Box<T> has no lifetime bound on T
type T1 = Box<dyn Foo>;
type T2 = Box<dyn Foo + 'static>;

// ...and so are these:
impl dyn Foo {}
impl dyn Foo + 'static {}

// ...so are these, because &'a T requires T: 'a
type T3<'a> = &'a dyn Foo;
type T4<'a> = &'a (dyn Foo + 'a);

// std::cell::Ref<'a, T> also requires T: 'a, so these are the same
type T5<'a> = std::cell::Ref<'a, dyn Foo>;
type T6<'a> = std::cell::Ref<'a, dyn Foo + 'a>;

// For the following trait...
trait Bar<'a>: 'a { }

// ...these two are the same:
type T1<'a> = Box<dyn Bar<'a>>;
type T2<'a> = Box<dyn Bar<'a> + 'a>;

// ...and so are these:
impl<'a> dyn Bar<'a> {}
impl<'a> dyn Bar<'a> + 'a {}
```

下面这个例子就是无法自动推断出来 Trait 对象的生命周期：

```rust
trait Foo {}

// This is an example of an error.
struct TwoBounds<'a, 'b, T: ?Sized + 'a + 'b> {
  f1: &'a i32,
  f2: &'b i32,
  f3: T,
}
type T7<'a, 'b> = TwoBounds<'a, 'b, dyn Foo>;
//                                  ^^^^^^^
// Error: the lifetime bound for this object type cannot be deduced from context

// 需要手动标注出来
type T7<'a, 'b, 'c> = TwoBounds<'a, 'b, dyn Foo + 'c>;
```

### Trait Object 例子一

```rust
trait A {
  fn value(&self) -> &str;
}

// 等价于 impl dyn A + 'static
impl dyn A {
  fn trait_value(&self) -> &str {
    self.value()
  }
}

fn extract1(a: &dyn A) -> &str {
  a.trait_value()
}

fn main() {}
```

这个例子是无法通过编译的。报错如下：

```shell
error[E0621]: explicit lifetime required in the type of `a`
  --> src/main.rs:22:5
   |
21 | fn extract1(a: &dyn A) -> &str {
   |                ------ help: add explicit lifetime `'static` to the type of `a`: `&'static (dyn A + 'static)`
22 |   a.trait_value()
   |     ^^^^^^^^^^^ lifetime `'static` required
```

### Trait Object 例子二

这个例子可以通过编译。

```rust
trait A {
  fn value(&self) -> &str;
}

// 等价于 impl dyn A + 'static
impl dyn A {
  fn trait_value(&self) -> &str {
    self.value()
  }
}

// 可以这么标注生命周期参数
fn extract<'t>(a: &'t (dyn A + 'static)) -> &'t str {
  a.trait_value()
}

fn main() {}
```

### Trait Object 例子三

这个例子可以通过编译。

```rust
trait A {
  fn value(&self) -> &str;
}

impl dyn A {
  fn trait_value(&self) -> &str {
    self.value()
  }
}

type DynA = dyn A;
fn extract3(a: &DynA) -> &str {
  a.trait_value()
}

fn main() {}
```

### Trait Object 例子四

这个例子可以通过编译。

```rust
trait A {
  fn value(&self) -> &str;
}

impl<'a> dyn A + 'a {
  fn trait_value(&self) -> &str {
    self.value()
  }
}

fn extract4<'t, 'm>(a: &'t (dyn A + 'm)) -> &'t str {
  a.trait_value()
}

fn main() {}
```

## HRTB -- Higher Ranked Trait Bound

### HRTB例子一

```rust
use std::fmt::Debug;

trait DoSomething<T> {
  fn do_sth(&self, value: T);
}

impl<'a, T: Debug> DoSomething<T> for &'a usize {
  fn do_sth(&self, value: T) {
    println!("{:?}", value);
  }
}

fn foo<'a>(b: Box<dyn DoSomething<&'a usize>>) {
  let s: usize = 10;
  b.do_sth(&s);
}

fn main() {
  let x = Box::new(&2usize);
  foo(x);
}
```

这个例子是无法通过编译的。报错如下，大概意思就是说 b 的生命周期比 s
的生命周期长，函数 foo 结束的时候 s 都被销毁了，还被引用着，所以报错。
但是实际上，`b.do_sth(&s)` 这行代码运行结束之后，就没有用到 s 了，
所以代码逻辑上应该是没有问题的。

```shell
error[E0597]: `s` does not live long enough
  --> src/main.rs:15:12
   |
13 | fn foo<'a>(b: Box<dyn DoSomething<&'a usize>>) {
   |        -- lifetime `'a` defined here
14 |   let s: usize = 10;
15 |   b.do_sth(&s);
   |   ---------^^-
   |   |        |
   |   |        borrowed value does not live long enough
   |   argument requires that `s` is borrowed for `'a`
16 | }
   | - `s` dropped here while still borrowed
```

解决方法见下一个例子。

### HRTB例子二

修改为下面这种语法之后，就不受限制了。

```rust
fn foo(b: Box<dyn for<'f> DoSomething<&'f usize>>) {
  let s: usize = 10;
  b.do_sth(&s);
}
```

### HRTB例子三

这个例子不太理解。

```rust
trait FooTrait {
  fn show(&self) {}
}

impl<'a> FooTrait for &'a dyn for<'b> FooTrait
where
  for<'b> dyn FooTrait: FooTrait,
{
  fn show(self: &&'a dyn for<'b> FooTrait) {
    println!("show 1")
  }
}

impl FooTrait for for<'a> fn(&'a dyn for<'b> FooTrait) {
  fn show(&self) {
    println!("show 2")
  }
}

fn global_test(x: &dyn for<'a> FooTrait) {
  (&x).show(); // show 1
  x.show(); // show 2
  <&dyn for<'a> FooTrait as FooTrait>::show(&x); // show 1
}

fn main() {
  let x = &(global_test as for<'a> fn(&'a dyn for<'b> FooTrait));
  global_test(x);
}
```

## 生命周期其他例子

### 例子一

这个例子可以通过编译。

```rust
trait Foo<'a> {}

struct FooImpl<'a> {
  s: &'a [u32],
}

impl<'a> Foo<'a> for FooImpl<'a> {}

fn foo<'a>(s: &'a [u32]) -> Box<dyn Foo<'a> + 'a> {
  Box::new(FooImpl { s: s })
}

fn main() {}
```

### 例子二

```rust
/*
pub trait Deref {
  type Target: ?Sized;
  pub fn deref(&self) -> &Self::Target;
}
pub trait DerefMut: Deref {
  pub fn deref_mut(&mut self) -> &mut Self::Target;
}

pub trait Clone {
  pub fn clone(&self) -> Self;
  pub fn clone_from(&mut self, source: &Self) { ... }
}
pub trait Copy: Clone { }

&'b mut &'a mut T
 -->(ok) &'b mut T
 -->(no) &'a mut T

pub fn deref_mut<'b>(&'b mut self) -> &'b mut Self::Target;
pub fn deref_mut<'b>(&'b mut &'a mut T) -> &'b mut T;

&'b mut &'a T
 -->(ok) &'b &'a T
 -->(ok) &'b T
 -->(ok) &'a T

pub fn clone<'b>(&'b self) -> Self;
pub fn clone<'b>(&'b &'a T) -> &'a T;
*/

use std::fmt::Debug;

fn foo<'a: 'b, 'b, T>(x: &'b mut &'a mut T)
where
  T: Debug,
{
  let a: &'b mut T = x;
  println!("foo a = {:?}", a);

  //let b: &'a mut T = x;
  //println!("b = {:?}", b);
}

fn bar<'a: 'b, 'b, T>(x: &'b mut &'a T)
where
  T: Debug,
{
  let a: &'b &'a T = x;
  println!("bar a = {:?}", a);

  let b: &'b T = x;
  println!("bar b = {:?}", b);

  let c: &'a T = x;
  println!("bar c = {:?}", c);

  //let d: &'a &'b T = x;
  //println!("bar d = {:?}", d);
}

fn main() {
  {
    let mut a = 10;
    foo(&mut &mut a);
  }

  {
    let mut a = 10;
    bar(&mut &a);
  }
}
```

## 相关资源


- [book/references-and-borrowing]
- [book/lifetime-syntax]
- [reference/lifetime-elision]
- [nomicon/lifetimes]
- [nomicon/hrtb]
- [RFC-0387-higher-ranked-trait-bounds]
- [RFC-0599-default-object-bound]
- [RFC-1156-adjust-default-object-bounds]
- [ZhangHanDong/inviting-rust]
- [how-does-for-syntax-differ-from-a-regular-lifetime-bound]
- [Rust生命周期]
- [一个关于rust生命周期的问题分析]



[book/references-and-borrowing]: https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html#references-and-borrowing
[book/lifetime-syntax]: https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html

[reference/lifetime-elision]:https://doc.rust-lang.org/reference/lifetime-elision.html

[nomicon/lifetimes]: https://doc.rust-lang.org/nomicon/lifetimes.html
[nomicon/hrtb]: https://doc.rust-lang.org/nomicon/hrtb.html

[RFC-0387-higher-ranked-trait-bounds]: https://github.com/rust-lang/rfcs/blob/master/text/0387-higher-ranked-trait-bounds.md
[RFC-0599-default-object-bound]: https://github.com/rust-lang/rfcs/blob/master/text/0599-default-object-bound.md
[RFC-1156-adjust-default-object-bounds]: https://github.com/rust-lang/rfcs/blob/master/text/1156-adjust-default-object-bounds.md

[ZhangHanDong/inviting-rust]: https://github.com/ZhangHanDong/inviting-rust
[how-does-for-syntax-differ-from-a-regular-lifetime-bound]: https://stackoverflow.com/questions/35592750/how-does-for-syntax-differ-from-a-regular-lifetime-bound/35595491#35595491
[Rust生命周期]: https://zhuanlan.zhihu.com/p/93846179
[一个关于rust生命周期的问题分析]: https://zhuanlan.zhihu.com/p/104742696
