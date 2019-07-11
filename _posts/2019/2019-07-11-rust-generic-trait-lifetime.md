---
layout: post
title:  "[Rust] generic, trait and lifetime"
date:   2019-07-11
comments: true
categories: Rust
tags: [Rust, generic, trait, lifetime]
description:
published: true
---

## Example 1

```rust
use std::fmt::Display;

fn longest_with_an_announcement<'a, T>(x: &'a str, y: &'a str, ann: T) -> &'a str
where
    T: Display,
{
    println!("Announcement! {}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

fn main() {
    println!("Hello, world!");
}
```

## Example 2

```rust
use std::fmt;

struct Point1<T> {
    x: T,
    y: T,
}

impl<T> Point1<T> {
    fn new(x: T, y: T) -> Self {
        Self { x, y }
    }

    fn x(&self) -> &T {
        &self.x
    }
}

impl<T: fmt::Display + PartialOrd> Point1<T> {
    fn cmp_display(&self) {
        if self.x > self.y {
            println!("The largest number is x = {}", self.x);
        } else {
            println!("The largest number is y = {}", self.y);
        }
    }
}

impl Point1<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}

#[derive(Debug)]
struct Point2<A, B> {
    x: A,
    y: B,
}

impl<A, B> Point2<A, B> {
    fn mixup<C, D>(self, other: Point2<C, D>) -> Point2<A, D> {
        Point2 {
            x: self.x,
            y: other.y,
        }
    }
}

fn main() {
    println!("Hello, world!");

    let integer = Point1 { x: 5, y: 10 };
    let float = Point1 { x: 1.0, y: 4.0 };
    println!("interger.x = {}", integer.x());
    println!("float.x = {}", float.x());
    println!(
        "float.distance_from_origin = {}",
        float.distance_from_origin()
    );

    // expected integer, found floating-point number
    // let wont_work = Point1 { x: 5, y: 4.0 };

    let p1 = Point2 { x: 5, y: 4.0 };
    let p2 = Point2 {
        x: "hello",
        y: "world",
    };
    let p3 = p1.mixup(p2);
    println!("p3 = {:?}", p3);
}
```

## Example 3

```rust
use std::fmt;

pub trait Summary {
    fn summarize(&self) -> String;

    fn new_summarize(&self) -> String {
        format!("(Read more from {}...)", self.summarize_author())
    }

    fn summarize_author(&self) -> String;
}

#[derive(Debug)]
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} {}", self.headline, self.author, self.location)
    }

    fn summarize_author(&self) -> String {
        String::from("")
    }
}

impl fmt::Display for NewsArticle {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "{}, by {} {}", self.headline, self.author, self.location)
    }
}

pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }

    fn summarize_author(&self) -> String {
        format!("@{}", self.username)
    }
}

impl fmt::Display for Tweet {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "{}: {}", self.username, self.content)
    }
}

impl Tweet {
    fn show(&self) {
        println!("{}: {}", self.username, self.content)
    }
}

/*
notify1, notify2, notify3 实现相同的行为。
*/
pub fn notify1(item: &impl Summary) {
    println!("notify1 Breaking news! {}", item.summarize());
}

pub fn notify2<T: Summary>(item: &T) {
    println!("notify2 Breaking news! {}", item.summarize());
}

pub fn notify3<T>(item: &T)
where
    T: Summary,
{
    println!("notify3 Breaking news! {}", item.summarize());
}

/*
notify4, notify5, notify6 实现相同的行为。
*/
pub fn notify4(item: &(impl Summary + fmt::Display)) {
    println!("notify4 Breaking news! {}", item);
}

pub fn notify5<T: Summary + fmt::Display>(item: &T) {
    println!("notify5 Breaking news! {}", item);
}

pub fn notify6<T>(item: &T)
where
    T: Summary + fmt::Display,
{
    println!("notify6 Breaking news! {}", item);
}

/*
bignotify1, bignotify2, bignotify3 实现相同的行为。
*/
pub fn bignotify1(
    T: &(impl Summary + fmt::Display),
    U: &(impl Summary + fmt::Display + fmt::Debug),
) {
    println!("bignotify1 Breaking news! \n\t{}\n\t{}", T, U);
}

pub fn bignotify2<T: Summary + fmt::Display, U: Summary + fmt::Display + fmt::Debug>(t: &T, u: &U) {
    println!("bignotify2 Breaking news! \n\t{}\n\t{}", t, u);
}

pub fn bignotify3<T, U>(t: &T, u: &U)
where
    T: Summary + fmt::Display,
    U: Summary + fmt::Display + fmt::Debug,
{
    println!("bignotify3 Breaking news! \n\t{}\n\t{}", t, u);
}

fn main() {
    let tweet = Tweet {
        username: String::from("horse_ebooks"),
        content: String::from("of course, as you probably already know, people"),
        reply: false,
        retweet: false,
    };

    tweet.show();
    println!("1 new tweet: {}", tweet.summarize());
    println!("1 new tweet: {}", tweet.new_summarize());

    let article = NewsArticle {
        headline: String::from("Penguins win the Stanley Cup Championship!"),
        location: String::from("Pittsburgh, PA, USA"),
        author: String::from("Iceburgh"),
        content: String::from(
            "The Pittsburgh Penguins once again are the best hockey team in the NHL.",
        ),
    };

    println!("New article available! {}", article.summarize());
    println!();

    notify1(&tweet);
    notify1(&article);

    notify2(&tweet);
    notify2(&article);

    notify3(&tweet);
    notify3(&article);

    println!();

    notify4(&tweet);
    notify4(&article);

    notify5(&tweet);
    notify5(&article);

    notify6(&tweet);
    notify6(&article);

    println!();

    bignotify1(&tweet, &article);
    bignotify2(&tweet, &article);
    bignotify3(&tweet, &article);
}
```
