---
title: Rust 快速入门指北[引用与借用]
categories:
  - Rust
tags:
  - Rust
excerpt: "引用与借用"
date: 2025/11/28 00:00:00
---
# Rust 快速入门指北[引用与借用]

<img src="https://s2.loli.net/2026/01/04/kA9wNqIzTfLeQ7s.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1767517753573" style="zoom: 67%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom:67%;" />

由于所有权和作用域的作用，在我们使用函数的时候可能会不方便：对像 `String` 这类**非 `Copy`** 类型，按值传参会发生 Move，从而导致所有权转移。有办法在**不转移所有权**的前提下还能对数据进行操作和处理吗？Rust 当然可以，接下来我们将通过介绍引用与借用来解释这里面的机制：

[TOC]



## 引用与可变引用

回到最早的例子：

```rust
fn take_ownership(some_string: String)-> String {
    println!("{}", some_string);
    some_string
}
fn main()  {
    let s1 = String::from("ownership test");
    let mut s2 = s1;
    s2 = take_ownership(s2);// 通过递交所有权和收回所有权来实现变量的延续
    println!("{s2}, function!");
}
```

我们可以提供一个 `String` 值的引用（reference）。**引用**（*reference*）像一个指针，因为它是一个地址，我们可以由此访问储存于该地址的属于其他变量的数据。与指针不同，引用在其生命周期内保证指向某个特定类型的有效值。

修改例子为：

```rust
fn borrow_string(some_string: &String) {  // 参数为 String 的引用（借用），不需要返回值
    // some_string.push_str(", world"); // 暂时不启用
    println!("{} function call", some_string);
}  // 这里，some_string 离开了作用域。但因为它并不拥有引用值的所有权，所以什么也不会发生


fn main() {
    let s1 = String::from("ownership test");
    let s2 = s1; // 这一行会发生 Move；这里保留 s2 只是为了和上一章例子呼应
    borrow_string(&s2);
    println!("{s2}, function!");
}
```

这里的`&s2`为s2的引用，这个 & 符号就是 **引用**，它们允许你使用值但不获取其所有权。其内存状态类似如下图1：

<img src="https://s2.loli.net/2025/11/27/gYf2IxValRN6yPL.png" alt="image-20251127211921949" style="zoom: 33%;" />

我们将创建一个引用的行为称为 **借用**（*borrowing*）。正如现实生活中，如果一个人拥有某样东西，你可以从他那里借来。当你使用完后，必须还回去。因为我们并不拥有它的所有权。

借用的时候，大多都是为了对被借用对象进行操作和更新，这里的例子我们同时也修改了`s2`为不可变对象，如果我们要对`s2`在函数里面进行更新和操作我们就要使用**可变引用**，其和可变变量一样，基于一个可变变量才能创建可变借用，我们修改例子：

```rust
fn borrow_string_mut(some_string: &mut String) { // 可变借用
    some_string.push_str(",  add something");
    println!("{} function inside", some_string);
} // 这里，some_string 离开了作用域。但因为它并不拥有引用值的所有权，所以什么也不会发生

fn main() {
    let s1 = String::from("ownership test");
    let mut s2 = s1; // 修改为可变变量
    borrow_string_mut(&mut s2); // 传入可变引用
    println!("{s2}, function out!");
}
```

输出：

```text
ownership test, add something function inside
ownership test, add something, function out!
```

可以看到 字符串添加了 `add something`的内容，你可能会好奇如果传入一个不可变的s2引用会怎么样，当然也是可以的，我们在函数里面不对它进行修改添加等操作，将一个可变变量以一个不可变变量的形式引用：

```text
warning: variable does not need to be mutable
 --> src/main.rs:9:13
  |
9 |         let mut s2 = s1;
  |             ----^^
  |             |
  |             help: remove this `mut`
  |
  = note: `#[warn(unused_mut)]` (part of `#[warn(unused)]`) on by default

warning: `massflow_accelerator` (bin "massflow_accelerator") generated 1 warning
```

Rust的编译很智能，能提示出来，可以直接定义为不可变变量。

可变引用有一个很大的限制：如果你有一个对该变量的可变引用，你就不能再创建对该变量的可变引用和不可变引用。尝试创建两个 `s` 的可变引用的代码会失败，但是创建多个不可变引用却是可以的，举个例子：

```rust
{
    let s1 = String::from("ownership test");
    let mut s2 = s1;

    let s3: &String = &s2; // 创建一个不可变引用
    let s4: &String = &s2; // 创建一个不可变引用

    println!("s2: {}, s3: {}, s4: {}", s2, s3, s4);
    borrow_string_mut(&mut s2);
    println!("{s2}, function out!");
}
```

如果修改为：

```rust
{
    let s1 = String::from("ownership test");
    let mut s2 = s1;

    let s3 = &mut s2; // 创建一个可变引用
    let s4: &String = &s2; // 创建一个不可变引用

    println!("s2: {}, s3: {}, s4: {}", s2, s3, s4);
}
```

则会报错：

```bash
   Compiling massflow_accelerator v0.1.0 (/home/neo/Desktop/Massflow_accelerator)
error[E0502]: cannot borrow `s2` as immutable because it is also borrowed as mutable
  --> src/main.rs:12:27
   |
11 |         let s3 = &mut s2; // 创建一个可变引用
   |                  ------- mutable borrow occurs here
12 |         let s4: &String = &s2; // 创建一个不可变引用
   |                           ^^^ immutable borrow occurs here
13 |         println!("s2: {}, s3: {}, s4: {}", s2, s3, s4);
   |                                                -- mutable borrow later used here

error[E0502]: cannot borrow `s2` as immutable because it is also borrowed as mutable
  --> src/main.rs:13:44
   |
11 |         let s3 = &mut s2; // 创建一个可变引用
   |                  ------- mutable borrow occurs here
12 |         let s4: &String = &s2; // 创建一个不可变引用
13 |         println!("s2: {}, s3: {}, s4: {}", s2, s3, s4);
   |                                            ^^  -- mutable borrow later used here
   |                                            |
   |                                            immutable borrow occurs here
   |
   = note: this error originates in the macro `$crate::format_args_nl` which comes from the expansion of the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info)

For more information about this error, try `rustc --explain E0502`.
error: could not compile `massflow_accelerator` (bin "massflow_accelerator") due to 2 previous errors
```

这一限制以一种非常小心谨慎的方式允许可变性，防止同一时间对同一数据存在多个可变引用。因为大部分语言中变量任何时候都是可变的。这个限制的好处是 Rust 可以在编译时就避免数据竞争。**数据竞争**（*data race*）类似于竞态条件，它可由这三个行为造成：

- 两个或更多指针同时访问同一数据。
- 至少有一个指针被用来写入数据。
- 没有同步数据访问的机制。

## 悬垂引用（Dangling References）

在具有指针的语言中，很容易通过释放内存时保留指向它的指针而错误地生成一个**悬垂指针**（*dangling pointer*）—— 指向可能已被分配给其他用途的内存位置的指针。相比之下，在 Rust 中编译器确保引用永远也不会变成悬垂引用：

常见的悬垂引用可以通过“作用域 + 所有权”相互影响的方式来展现：

```rust
fn main() {
    let reference_to_nothing = dangle();// dangle 返回一个字符串的引用
}

fn dangle() -> &String {
    let s = String::from("hello");// s 是一个新字符串

    &s// 返回字符串 s 的引用
}// 这里 s 离开作用域并被丢弃。其内存被释放。
```

报错如下：

```bash
$ cargo run
   Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0106]: missing lifetime specifier
 --> src/main.rs:5:16
  |
5 | fn dangle() -> &String {
  |                ^ expected named lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is no value for it to be borrowed from
help: consider using the `'static` lifetime, but this is uncommon unless you're returning a borrowed value from a `const` or a `static`
  |
5 | fn dangle() -> &'static String {
  |                 +++++++
help: instead, you are more likely to want to return an owned value
  |
5 - fn dangle() -> &String {
5 + fn dangle() -> String {
  |

error[E0515]: cannot return reference to local variable `s`
 --> src/main.rs:8:5
  |
8 |     &s
  |     ^^ returns a reference to data owned by the current function

Some errors have detailed explanations: E0106, E0515.
For more information about an error, try `rustc --explain E0106`.
error: could not compile `ownership` (bin "ownership") due to 2 previous errors
```

在报错信息里面涉及到了生命周期这个概念，目前还没介绍，后边会详细介绍。

生命周期，作用域，借用，所有权等几个概念共同构建了Rust的内存管理部分，如果把C++的内存管理称之为手动模式，Python/Java称为自动模式，Rust就是半自动模式，其通过上述几个概念的相互作用最终构成了这种半自动模式。

这里如想让代码正常运行，修改为返回s即可：

```rust
fn no_dangle() -> String {
    let s = String::from("hello");

    s
}//s的所有权被转移
```

## 总结

- 在任意给定时间，**要么**只能有一个可变引用，**要么**只能有多个不可变引用。
- 引用必须总是有效的。
- 生命周期，作用域，借用，所有权等几个概念共同构建了Rust的内存管理部分
