---
title:  Android开发笔记——快速入门（Kotlin入门）
categories:
  -  Android
  -  Kotlin
tags:
  -  Android
  -  Kotlin
excerpt: "Kotlin入门"
---

# Android开发笔记——快速入门（Kotlin入门）

<img src="https://s2.loli.net/2024/09/29/gC5eQ3URr1qGApZ.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727617280095" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />



#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]

# Why Kotlin？

### Kotlin的发展历史

2011年，JetBrains 发布了Kotlin的第一个版本，并在2012年将其开源。

2016年Kotlin发布了1.0正式版，代表着Kotlin语言已经足够成熟和稳定了，并且JetBrains 也在自家的旗舰IDE开发工具Intelli]IDEA中加入了Kotlin的支持。

2017年Google 宣布 Kotlin 正式成为Android开发一级语言，并且Android Studio 也加入了对 Kotlin的支持。

2019年Google正式宣布了Kotlin First，未来提供的官方API会以Kotlin为主。

**Kotlin**作为**Jetbrain**全新开发的语言，虽然是基于**JVM**进行的开发但是却是**JAVA**的温和改良版本，如果说C是一个国色天香的感觉，那么**Java**就是窈窕淑女而**Kotlin**就是小家碧玉，端庄优雅的温柔。

**Kotlin**是一个极其成功的开源项目，代表了目前编程语言的一个发展方向，**Kotlin**在编写代码时有如下优势：代码简洁高效、函数式编程、空指针安全、支持**lambda**表达式、流式API等。光说起来你会感觉很难懂到底在说什么，但是当你跨进**Kotlin**大门之后才能体会到运用**Kotlin**编程的快乐。

### Kotlin和java的比较优点

Kotlin是完全和java兼容的，怎么个兼容办法呢？

Kotlin可以做到和Java 100%兼容，这主要是得益于Java虚拟机的工作机制 ，其实Java虚拟机并不会直接和你编写的Java代码打交道，而是和编译之后生 成的class文件打交道 ，而Kotlin也有一个自己的编译器，它可以将Kotlin代码也编译成同样规格的 class文件 ，Java虚拟机不会关心class文件是从Java编译来的，还是从Kotlin编译来的， 只要是符合规格的class文件，它都能识别 ，也正是这个原因，JetBrains才能以一个第三方公司的身份设计出一门用来 开发Android应用程序的编程语言

Kotlin是完全兼容Java现有的代码库的，也就是说你可以跨语言调用对应的代码内容，甚至于还有C、C++互操作，更神奇的是Kotlin也可以完成Javascript的工作，他编写的代码可以编译成JS的代码来运行！

## 如何使用Kotlin开发Android？

首先你需要安装**Android Studio**来进行开发，详细安装过程可以参考：[Android开发笔记——快速入门（壹)](https://blog.csdn.net/qq_20540901/article/details/123426867)

根据教程创建一个基于**Kotlin**的Android项目：

具体如何创建项目可以参考：[上一篇文章](https://blog.csdn.net/qq_20540901/article/details/123458305?spm=1001.2014.3001.5502)。

创建完成以后，在相同包下创建一个Kotlin类：

![1](https://s3.bmp.ovh/imgs/2022/06/20/8b4f5bf6b35badac.png)



Kotlin class的创建：

![2](https://s3.bmp.ovh/imgs/2022/06/20/acc3bdd49432536e.png)

### Kotlin的变量与函数

创建完成以后就可以愉快的，使用AS来进行kotlin的编写：

首先创建一个主函数：

```Kotlin
package com.example.codewithkotlin


fun main()
{
    println("Hello Kotlin!")
}
```

你可以点击左边的小箭头运行一下

![3](https://s3.bmp.ovh/imgs/2022/06/20/d8f09d31972d0ae5.png)

#### kotlin的变量

kotlin的变量与Java有很大的不同，可以总结如下两点：

- 定义时不需要指定类型，而是需要指定变量是否可变。
- 没有保留Java基本的数据类型，而是使用对象数据类型，在 Kotlin 中，所有东西都是对象。

举例：

不需要指定变量类型，其会自动推导：

```kotlin
fun main()
{
    val a=10
    println("a is " + a)
}
```

指定数据类型时不会再自动推导：

```kotlin
fun main()
{
    val a : Int=10
    println("a is " + a)
}
```

输出结果：

```shell
a is 10
```

val与var是用来指定是否为变量的关键字，val类似于java的final，var就是常规的变量，为什么要这样设计你如果有编程基础你就知道变量是不安全的，val，var就是为了指定这个变量是否可变（强制要求显式定义），这有一个小技巧，定义变量时应该是都定义为val，只有你需要var的时候再回来将这个变量改定义为var，这样就保证了基本所有变量的安全。

#### Kotlin的函数

kotlin的函数与Java中的方法其实很像，但是更自由，书写更方便。

fun（function的简写）是定义函数的关键字，无论你定义什么函数，都一定要使用fun来声明。当然这不包含函数表达式。

其基本定义格式如下：

```kotlin
fun funName(param1 : Int,param2 :Int) ：returnvaluetype 
{
	return value
}
```

如果没有返回参数就可以省略`：returnvaluetype` 部分，从这里看出Kotlin的设计思想：

**能省就省**

简化以后就可以表现为：

```kotlin
fun funName(param1 : Int,param2 :Int)
{
}
```

为了保持设计思想，Kotlin做出了更多的省略,当你的函数体只有一块作为返回值的代码时，可以直接省略大括号，用等号来连接,这里用一个返回最大值的函数来表示：

```kotlin
fun maxNumber(num1 :Int ,num2 :Int):Int = max(num1,num2)
```

但是我们说过Kotlin具有出色的推导机制，那么他能不能推断出这里一定返回的是和max函数返回相同的内容呢？当然可以。所谓我们进一步简化省略去返回值：

```kotlin
fun maxNumber(num1 :Int ,num2 :Int)= max(num1,num2)
```

使用Kotlin以后你就会发现能在一行就实现一个完整的函数，但这并不是我们想要的，因为这里我们只是对MAX函数进行了一个包装，里面并没有任何逻辑部分。下面我们来写一下如何写逻辑部分的内容，并对其进行简化，进一步体验其强大性。

### Kotlin的逻辑控制

#### Kotlin的if

if在使用的时候与传统的if并没有太大区别，但是他还有一个额外的功能，可以有返回值。为什么要给if来整一个返回值呢？返回值常常与函数相关，对于现在流行的函数式编程，Kotlin也对其做出了支持，if的返回值就是如此，**if语句会自动返回其最后一条语句的返回值**：

常规IF的写法如下：

```kotlin
fun maxNumber(num1 :Int ,num2 :Int) ：Int
{
    var maxnumber =0
    if(num1 >= num2)
        maxnumber = num1
    else
        maxnumber = num2
    return maxnumber
}
```

使用其返回值如下，是不是看起来更简略一点了呢：

```kotlin
fun maxNumber(num1 :Int ,num2 :Int):Int
{
    val maxnumber = if(num1 >= num2)
                        num1
                    else
                        num2
    return maxnumber
}
```

其实还可以再简略一下我们可以把if和else看成一个语句，这样他就可以直接给函数返回内容，我们就可以省略函数体的括号和返回值类型：

```kotlin
fun maxNumber(num1 :Int ,num2 :Int)=if(num1 >= num2)num1 else num2
```

这样就实现了最简洁的写法。

#### Kotlin的when

Kotlin的when就是Java，C中的Switch case，负责的是样例匹配的工作，当你需要匹配内容很多的时候用if，是很不优雅的，但是Kotlin的when提供了一种简介方便的方式。

我先提供一个写法：

```kotlin
fun getGender(name : String) =
    when (name)
    {
        "zzy"-> println("boy")
        "lxl"-> println("girl")
        "hrq"-> println("boy")
        else ->  println("not a boy or girl")
    }
```

这段代码给我们提供了一个样例，我们通过传入任意一个类型的变量来使用when来进行匹配，when后面的括号就是要匹配的对象，下边的函数体就是要匹配的内容，你可以发现他取消了case等关键字而是使用**“->”**来作为关键字，意义表达明确，而且你不用担心执行完成后没有**break**会自动向下运行，每一个匹配后会执行完自己的逻辑代码然后退出。值得提醒的是你的逻辑代码只有一行的时候可以省略“{}”。

除了数据匹配之外，when语句还可以进行类型匹配，没错就是你想的那个数据类型，如果你想起来Kotlin中没有基本的数据类型，而是全部都是类数据，你就会意识到为什么要这样设计数据类型，为了类型匹配只是一小部分优点。

下面举一个例子：

```kotlin
fun getType(number : Number) =
    when (number)
    {
        is Int -> println("Int type")
        is Float-> println("Float type")
        is Long-> println("Long type")
        else ->  println("not my type")
    }
```

通过设置输入的number为所有数字类型的父类Number，你可以输入任意类型的数据，然后使用**is**关键字来检测是不是对应的类型。

你可以看到When里边都是同一种类型的匹配模式，但是如果要使用不同的匹配模式呢，打个比方我一个想要分离出名字里面有是zzy的，另一个想要分离出名字是lxl怎么办？很简单这里去掉when后边的括号就好了，将匹配表达式写入大括号里面。

```kotlin
fun getGender2(name : String) =
    when
    {
        name.contentEquals("zzy")-> println("a part of zzy")
        name === "lxl"-> println("girl")
        else ->  println("not Anyone")
    }
```

### Kotlin的循环

在介绍循环之前先介绍一个新的东西，区间

区间这个东西很简单，它的作用就是按照规律生成一组数列，我们举个例子试一试：

```kotlin
fun rangeTest()
{
    val range = 1..10
    val untils = 0 until 10
    val downtos = 10 downTo 1
    println(range)
    println(untils)
    println(downtos)
}
```

输出结果：

```shell
1..10
0..9
10..1
```

你可以看见这两个区间生成的数列区别，一个是包含左右边界，一格式只有左边界没有右边界。

有了区间以后就可以使用for来迭代，for循环在Kotlin中有了巨大的变化，我先来展示一下：

```kotlin
fun rangeTest()
{
    val range = 1..10
    val untils = 0 until 10
    println(range)
    println(untils)
    for (i in 10 downTo 1)
        println(i)
}
```

输出：

```shell
1..10
0..9
10
9
8
7
6
5
4
3
2
1
```

你可以看到了一个和java c都不太像的for 更想java里面的增强for循环，你就意识到这里for转化为了一个迭代器的作用。在后边我再继续讲解for的强大之处。



















































