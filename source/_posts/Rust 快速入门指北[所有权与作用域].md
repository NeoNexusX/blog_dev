---
title: Rust 快速入门指北[所有权与作用域]
categories:
  - Rust
tags:
  - Rust
excerpt: "所有权与作用域"
date: 2025/11/28 00:00:00
---

# Rust 快速入门指北[所有权与作用域]

<img src="https://s2.loli.net/2026/01/04/TinylHVZpO3kxae.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1767517637848" style="zoom: 67%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom:67%;" />

## 所有权（*ownership*）

所有权是一系列Rust用于内存管理的规则，相较于Python/Java的垃圾回收机制，Rust则通过所有权规则来对内存的使用和回收进行管理。

### Rust所有权规则

Rust的所有权遵循以下规则：

- Rust 中的每一个值都有一个 所有者（owner）。
- 值在任一时刻有且只有一个所有者。
- 当所有者离开作用域，这个值将被丢弃。

## 作用域

作用域指的是一个项（item）在程序中有效的范围。

假设在一个代码块**(Block)**:

```rust
{
    // 新的作用域开始
    let s1 = "hello"; // 此处 s1 开始有效
    println!("{}", s1);
    //作用域结束
}
```

## 程序内存的分配机制

系统的内存类型可分为：

- **全局段**（Global segment）：负责存储全局变量和静态变量，生命周期等于程序执行的整个时间段。
- **代码段**（Code segment）：包含组成我们程序的实际机器代码或指令，包括函数和方法。
- **栈**（Stack）[有些错误的叫为 **堆栈**，早期翻译的旧称，实际上是不正确的]。用于局部变量、函数参数等。
- **堆**（Heap）：灵活区域来存储大型数据，动态生命周期，在运行时按需分配或者释放。

<img src="https://s2.loli.net/2025/11/27/BQir9Z5JvSbDxE1.png" alt="image-20251127111940172" style="zoom:20%;" title="图1，程序内存分段"/>

在现代操作系统（Windows, Linux, macOS）中，每个程序启动时，操作系统都会给它分配一个独立的**虚拟内存空间**。也就是说程序拥有**自己独立的堆区域（逻辑上），但这个区域的“内存”是由操作系统授予的（物理上）。**操作系统管理所有的物理内存，它不关心你具体怎么用，它只负责按“页”（Page，通常是 4KB 大小）为单位，把内存“批发”给各个程序，当你的程序向系统申请内存的时候，操作系统就再划拨几页虚拟内存给你。

上图1展示了，一个程序的内存的逻辑分段，当中的箭头表明了其扩展方向，当然操作系统不同其扩展方向不同，扩展方向分为：

- **上（Up）**：指 **高地址**（High Address），例如 `0xFFFFFFFF`。
- **下（Down）**：指 **低地址**（Low Address），例如 `0x00000000`。

在Linux/x86下其模型可能是，下图2的形式，我们可以看到，Stack和Heap的位置发生了反转，不过这并不重要，重要的是二者延展方向是相对的。

<img src="https://s2.loli.net/2025/11/27/UxkVCz7T5t4rAgl.webp" alt="img" style="zoom:80%;" />

按照上图的Linux主线程模型来说：

- 栈的起始位置通常在内存的高地址区。当你往栈里压入数据（比如调用一个新的函数，或者声明一个新的局部变量）时，栈顶指针（Stack Pointer）会向 **低地址** 移动。
- 堆的起始位置通常在内存的低地址区（在程序代码和静态数据区之上）。当你申请新的动态内存（如 Rust 的 `String::from` 或 C 的 `malloc`）时，堆的边界会向 **高地址** 移动。
- 内存布局中，栈从高地址向下增长，堆从低地址向上增长，中间是空闲区。只要两者不碰头，程序就能运行。在实际中，由于64位系统虚拟内存空间极大，两者几乎不可能碰头。更常见的内存问题是：**物理内存耗尽（OOM）**，这通常发生在堆上，即申请的内存超过了物理内存上限；以及**栈溢出**，这是触碰了操作系统为栈设置的较小固定上限（如8MB），通常由无限递归等导致。

堆和栈介绍：

- [知乎，栈和堆的区别](https://zhuanlan.zhihu.com/p/647302641) 
- [知乎，栈和堆的延伸方向](https://www.zhihu.com/question/36103513)

## 堆上数据的所有权和作用域

上述内容的代码块里面，`"hello"` 是一个字符串字面量（`&'static str`），其字符串内容通常存放在程序的只读静态区域（可粗略理解为“全局/静态区”），生命周期贯穿整个程序运行。

需要注意的是：`let s1 = "hello";` 这行代码里，栈上保存的是一个**字符串切片**（包含指针+长度，指向静态区的那段只读字符串），而不是把字符串内容本身拷贝到栈上。离开作用域时，`s1` 这个切片从栈上消失即可；而静态区的字符串字面量不会被释放。与之相对的堆数据变化较多，我们接下来将使用堆数据进行更详细的所有权和作用域介绍。

Rust 有另一种字符串类型，`String`。这个类型管理被分配到堆上的数据，所以能够存储在编译时未知大小的文本。可以使用 `from` 函数基于字符串字面值来创建 `String`，如下：

```rust
{
    let mut s = String::from("hello"); // 从此处起，s 是有效的
    println!("{}", s);
    s.push_str(", world!");
    println!("{}", s);  // 使用 s
} // 此作用域已结束，s 不再有效
```

### 堆变量的交互和移动

在Rust中，堆变量的交互和移动与栈变量有很大区别，我们回顾下普通的栈变量：

```rust
{
    let x = 5; // x 在此作用域内有效
    let y = x + 2;
    println!("y 的值是: {}", y);
}
```

上述操作可以理解为：“将 `5` 绑定到 `x`；接着生成一个值 `x` 的拷贝并绑定到 `y`”

但是在String里面情况发生了很大区别：

```rust
{
    let s1 = String::from("hello");
    let s2 = s1;
    println!("{s1}, world!");
}
```

<img src="https://s2.loli.net/2025/11/27/YmHyB8qvA364cZC.png" alt="image-20251127173444714" style="zoom:33%;" />

上图3展示了内存里面的结构形式，长度表示 `String` 的内容当前使用了多少字节的内存。容量是 `String` 从分配器总共获取了多少字节的内存。长度与容量的区别是很重要的，不过在当前上下文中并不重要，所以现在可以忽略容量。

当我们将 `s1` 赋值给 `s2`，这里发生的是 **移动（move）**：

- 底层表现为：把 `String` 在栈上的三元信息（指针、长度、容量）拷贝到 `s2`；
- 但堆上的字符串内容**不会**被拷贝；
- 同时，为避免二次释放，Rust 会让 `s1` 在语义上立即失效（不能再使用）。

内存中数据的表现如下图4所示。

<img src="https://s2.loli.net/2025/11/27/NKTaImOycp3UlbH.png" alt="image-20251127173712663" style="zoom:33%;" />

这和栈变量的复制形式有很大区别，栈变量的情况更类似如下图5：

<img src="https://s2.loli.net/2025/11/27/jnxq5pw1yAEg3Jl.png" alt="image-20251127174005558" style="zoom:33%;" />

按照我们原来的所有权释放内存的[规则](###Rust所有权规则)：

```rust
    {
      //进入作用域
        let s1 = String::from("hello"); //此处S1生效
        let s2 = s1; //此处S2生效
        println!("{s1}, world!");
    }// S1 和 S2 不再生效
```

当变量离开作用域后，Rust 自动调用 `drop` 函数并清理变量的堆内存。不过图 4 展示了两个数据指针指向了同一位置。这就有了一个问题：当 `s2` 和 `s1` 离开作用域，它们都会尝试释放相同的内存。这是一个叫做 **二次释放**（*double free*）的错误，也是之前提到过的内存安全性 bug 之一。两次释放（相同）内存会导致内存污染，它可能会导致潜在的安全漏洞。

当然Rust在所有权的设计上做了一些调整，从而避免了这种情况的出现。实际上上述代码是无法通过编译的，其报错如下：

```bash
Compiling massflow_accelerator v0.1.0 ()
warning: unused variable: `s2`
 --> src/main.rs:9:13
  |
9 |         let s2 = s1;
  |             ^^ help: if this is intentional, prefix it with an underscore: `_s2`
  |
  = note: `#[warn(unused_variables)]` (part of `#[warn(unused)]`) on by default

error[E0382]: borrow of moved value: `s1`
  --> src/main.rs:10:20
   |
 8 |         let s1 = String::from("hello");
   |             -- move occurs because `s1` has type `String`, which does not implement the `Copy` trait
 9 |         let s2 = s1;
   |                  -- value moved here
10 |         println!("{s1}, world!");
   |                    ^^ value borrowed here after move
   |
   = note: this error originates in the macro `$crate::format_args_nl` which comes from the expansion of the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info)
help: consider cloning the value if the performance cost is acceptable
   |
 9 |         let s2 = s1.clone();
   |                    ++++++++

For more information about this error, try `rustc --explain E0382`.
warning: `massflow_accelerator` (bin "massflow_accelerator") generated 1 warning
error: could not compile `massflow_accelerator` (bin "massflow_accelerator") due to 1 previous error
```

报错的关键有两部分：

```bash
 8 |         let s1 = String::from("hello");
   |             -- move occurs because `s1` has type `String`, which does not implement the `Copy` trait
 9 |         let s2 = s1;
   |                  -- value moved here
10 |         println!("{s1}, world!");
   |                    ^^ value borrowed here after move
```

其中一个是`s1` has type `String`, which does not implement the `Copy` trait，另一个是 `value borrowed here after move`

造成第二个报错的原因是变量进行了**移动**(move)操作，移动之后变量就无效了，我们对移动操作的定义如下：

类似如下的操作，我们都统称为**移动**：

<img src="https://s2.loli.net/2025/11/27/IHzEGwLNg4yoAmP.png" alt="image-20251127194509650" style="zoom:33%;" />

其操作更像是浅拷贝，但实际上是拷贝指针、长度和容量而不拷贝数据，相较于拷贝操作，移动操作并没有保留第一个变量，所以其并不等价于浅拷贝。

这样在移动之后，s1无效了，同时也避免了我们刚才所说的**二次释放**问题。

### 可变与不可变变量的移动

上述过程中的移动操作都是建立在相同类型的变量下的，为了验证不可变与可变类型的转换，我们将撰写一个例子，输出可变之前的数据地址和堆地址，查看在移动过程中地址是否发生了改变：

```rust
{
    let s1 = String::from("ownership test");

    // 验证：打印 s1 指向的堆数据地址
    println!("s1 指向的堆地址: {:p}", s1.as_ptr());
    // 验证：打印 s1 变量本身的栈地址
    println!("s1 变量的栈地址: {:p}", &s1);

    let mut s2 = s1;
    // 此时 s1 已经失效 (Move)，不能再打印 s1 了

    // 验证：打印 s2 指向的堆数据地址 (应该与 s1 的一样)
    println!("s2 指向的堆地址: {:p}", s2.as_ptr());
    // 验证：打印 s2 变量本身的栈地址 (应该与 s1 的不一样)
    println!("s2 变量的栈地址: {:p}", &s2);
    //修改s2的堆上内容
    take_ownership(&mut s2);
    println!("{s2}, function out!");
}
```

输出：

```text
s1 指向的堆地址: 0x1047c1d60
s1 变量的栈地址: 0x16bd261f8
s2 指向的堆地址: 0x1047c1d60
s2 变量的栈地址: 0x16bd262d0
ownership test, add something function inside
ownership test, add something, function out!
```

我们发现将不可变变量移交到可变变量后，变量本身（栈上的绑定/地址）发生了更新，但其指向的堆数据没有发生变化，且变得可操作了。

### copy 和 clone

关于第一个报错的原因，在讨论之前我们先说明一下，如何实现类似于图5的复制操作，也就是我们常说的深拷贝的操作。

如果我们 **确实** 需要深度复制 `String` 中堆上的数据，而不仅仅是栈上的数据，可以使用一个叫做 `clone` 的常用方法：

```rust
    let s1 = String::from("hello");
    let s2 = s1.clone();

    println!("s1 = {s1}, s2 = {s2}");
```

这样两个变量都会同时存在，但这样会造成性能的浪费，相当消耗资源。

在堆操作的数据往往如此，与此相对的对栈上的数据我们进行拷贝的时候不需要调用clone方法的：

```rust
  {
      let x = 5; // x 在此作用域内有效
      let mut y = x;
      y = y + 1;
      println!("x 的值是: {x},y 的值是: {y}");
  }
output ：x 的值是: 5,y 的值是: 6
```

对于整型（如 `i32`）来说，**深拷贝和浅拷贝没有区别**。整型的大小是固定的，完全存储在**栈（Stack）**上。整型不像**String**那样由“栈上的指针”指向“堆上的数据”，整型变量本身就持有数据的值。对于这一类数据长度固定的数据，没有深浅拷贝的区别，所以这里调用 `clone` 并不会与通常的浅拷贝有什么不同，我们可以不用管它。

当然，这样的实现也是依赖于另一个叫做 `Copy` trait 的特殊注解，可以用在类似整型这样的存储在栈上的类型上，如果一个类型实现了 `Copy` trait，那么一个旧的变量在将其赋值给其他变量后仍然有效。

同时Rust 不允许自身或其任何部分实现了 `Drop` trait 的类型使用 `Copy` trait。如果我们对其值离开作用域时需要特殊处理的类型使用 `Copy` 注解，将会出现一个编译时错误，这里实际上就能理解第一个报错： `move occurs because s1 has type String, which does not implement the Copy trait`,因为这里的**String**实际上持有`Drop` trait 。

任何不需要分配内存或某种形式资源的类型都可以实现 `Copy`，如下是一些 `Copy` 的类型：

- 所有整数类型，比如 `u32`。
- 布尔类型，`bool`，它的值是 `true` 和 `false`。
- 所有浮点数类型，比如 `f64`。
- 字符类型，`char`。
- 元组，当且仅当其包含的类型也都实现 `Copy` 的时候。比如，`(i32, i32)` 实现了 `Copy`，但 `(i32, String)` 就没有。

## 所有权和函数

将值传递给函数与给变量赋值的原理相似。向函数传递值可能会**移动**或者**复制**操作,触发操作的时候所有权产生变化，举个例子：

```rust
fn main() {
      let s1 = String::from("ownership test");
      let s2 = s1;
      take_ownership(s2); //移动了ownership至函数
      // println!("{s2}, world!"); // 没有启用
  }

fn take_ownership(some_string: String) {
    println!("{}", some_string);
} // 此作用域结束，some_string 不再有效，some_string 移出作用域并调用 `drop` 方法。
  // 占用的内存被释放

output：ownership test
```

如果我们启用，则会有以下报错：

```bash
   Compiling massflow_accelerator v0.1.0 (/Users/neo/Desktop/massflow_accelerator)
error[E0382]: borrow of moved value: `s2`
  --> src/main.rs:11:20
   |
 9 |         let s2 = s1;
   |             -- move occurs because `s2` has type `String`, which does not implement the `Copy` trait
10 |         take_ownership(s2);
   |                        -- value moved here
11 |         println!("{s2}, world!");
   |                    ^^ value borrowed here after move
   |
note: consider changing this parameter type in function `take_ownership` to borrow instead if owning the value isn't necessary
  --> src/main.rs:22:32
   |
22 | fn take_ownership(some_string: String) {
   |    --------------              ^^^^^^ this parameter takes ownership of the value
   |    |
   |    in this function
   = note: this error originates in the macro `$crate::format_args_nl` which comes from the expansion of the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info)
```

这也是Rust比较麻烦的地方，在其他编程语言里面，变量的生命周期不会这么转瞬即逝，不会随着作用域的变化而产生变化，**即从调用函数的较大作用域进入较小的函数作用域的时候其变量本身的生命周期并没有发生变化**。**但是Rust恰恰相反，其生命周期与所有权，作用域等都存在一定关系，多个特性共同决定了变量的生命周期**。

### 返回值和作用域

返回值也可以转移所有权。示例 展示了一个返回了某些值的示例:

```rust
fn take_ownership(some_string: String)-> String {
    println!("{}", some_string);// some_string 进入作用域
    some_string // 返回 some_string 并将其移至调用函数
} 

fn main(){
    let s1 = String::from("ownership test");
    let mut s2 = s1; //这里发生了第一次 Move。s1 的所有权移动给了 s2，s1 失效。
    s2 = take_ownership(s2); //移交所有权并重新获取所有权
    println!("{s2}, function!");
}
output：ownership test, function!
```

在 Rust 中，这里的 s2从技术上讲是一个**全新的变量绑定**，尽管它可能指向相同的堆内存数据（取决于编译器的优化），但在语义上发生了所有权的转移（Move）。

函数调用`take_ownership(s2)`这里发生了第二次 Move。s2 的所有权移动进了函数 take_ownership 的参数 some_string中。此时，外部的 s2（在赋值前）暂时失去了对数据的所有权。

## 总结

变量的所有权总是遵循相同的模式：将值赋给另一个变量时它会移动。当持有堆中数据值的变量离开作用域时，其值将通过 `drop` 被清理掉，除非数据被移动为另一个变量所有。虽然这样是可以的，但是在每一个函数中都获取所有权并接着返回所有权有些啰嗦。如果我们想要函数使用一个值但不获取所有权该怎么办呢？

