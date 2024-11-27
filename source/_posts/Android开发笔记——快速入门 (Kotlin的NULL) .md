---
title:  Android开发笔记——快速入门（Kotlin的NULL）
categories:
  -  Android
  -  Kotlin
tags:
  -  Android
  -  Kotlin
excerpt: "Kotlin的NULL"
date: 2024/10/22 00:00:00
---

# Android开发笔记——快速入门 (Kotlin与NULL)

<img src="https://s2.loli.net/2024/09/29/9rGTdRm4giweluL.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727617602165" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]

## Kotlin的空指针与辅助工具

### Kotlin的判空

在写java或者c的时候最让人难受的就是空指针的问题了，当你写了一大段代码，运行时来一个报错，心态是很爆炸的，怎么又找不到变量在哪里了？但是你如果正常情况下书写kotlin就不会遇到这些问题，为什么呢？

因为kotlin将判空过程不是放在运行时，而是放在了编译过程中，甚至有些在kotlin语言设计的时候就已经在考虑如何避免了，说起来可能很复杂，接下来详细介绍一下kotlin的判空机制。

先来看一段代码：

```kotlin
class Zzy(val gender: String = "Boy") : Person(22,"zhuzeyu")
{
    constructor(iq:Int, eq:Int) : this("man")
    {
        println("his iq is "+iq+" eq is " + eq)
    }
    init
    {
        println(name+" is a "+gender+" age is "+age)
    }
}

fun main(args: Array<String>)
{
    //koltin 空指针检查 //这段代码没有空指针风险
    val zzy = Zzy("boy")
}

```

我们构造了一个类并且继承了Person类，最后在主函数中调用这个类的构造方法，需要注意的是构造方法需要传入的一个字符串，如果传入的是一个空的字符串这里就会产生空指针问题，在java中是不给你任何提示的，但是在kotlin中就有所不同：

![image-20220408215120939](https://s2.loli.net/2024/09/29/ob2dVHwq4IxAWSU.png)

可以看到kotlin因为传入空而报错，这不是最重要的，因为这里显式的传入了null，那如果我们传入的是一个变量呢？

这里为了方便演示再传入一段代码：

```kotlin
fun main(args: Array<String>)
{
    //koltin 空指针检查 //这段代码没有空指针风险
    val zzy = Zzy(null)
    var fake_zzy :String = null;
}
```

实际上`var fake_zzy :String = null`是不能运行的，kotlin给出了错误提示：

![image-20220408222048233](https://s2.loli.net/2024/09/29/ASvpMjH15egbKnd.png)

编译器知道你这里传入就是一个null，所以代码实际上是不能运行的。

可以看到Kotlin从编译层面就避免了空指针问题的发生，但是这就意味着null在kotlin中就没有用处了吗？当然不是，接下来来介绍kotlin的可空类型。

### Kotlin的可空类型

如果在某个情况下我需要根据传入的变量的情况（空或者不空）来进行操作，但是你意识到这里面所有的传递参数都不能是空，甚至连编译都没办法通过这怎么办？

kotlin其实提供了可空类型

我们来看一个例子：

![image-20220408221845089](https://s2.loli.net/2024/09/29/q7HRpfNolbKTBs8.png)

你注意到String后边添加了一个？这事 String的变量不再报错了，对这就是可空的String类型。

当然不止String有，每个kotlin的类型都可以声明为可空类型，基本结构如下：

```kotlin
var 变量名 ： 变量类型 ? = null
```

当然你也许也注意到了方法传入的参数也被报错了，

其中test类是用来创建测试函数的，函数具体内容如下：

```kotlin
fun eat_empty_food (person: Person) = 
    if (person != null) 
        person.eat_empty() 
    else
        "empty"
```

可以看到此方法接受一个Person类作为参数，而zzy类继承了People类正好可以作为参数传递进去，但是这里我们传入了一个null，编译器报错了，为什么呢？因为参数也需要设置成可空的类型：

```kotlin
fun eat_empty_food (person: Person?) = 
	if (person != null) 
		person.eat_empty() 
	else 
		"empty"
```

这段代码什么意思呢？就是如果传进来的参数不是null的就正常调用一个方法，如果是空的就返回empty字符串。

是不是很简单呢？这是kotlin为了避免空指针问题而做的隔离，完全将空指针和正常变量做了一个隔离。

### Kotlin的判空辅助工具

在上面我们介绍了kotlin如何将空指针赶尽杀绝，但是我们有些时候也需要通过空指针来判断一些具体情况，Kotlin同时提供了一套完整、便捷的判断工具。

#### `?.`

首先介绍：`?.`

在上一节，如果你更改了参数为可空类型，实际上他还会报错，因为这时候函数使用的时候，就有可能遇到null问题，如何更改呢？就要使用`?.`。

```kotlin
fun eat_empty_food_update (person: Person?) = person?.eat_empty()
```

`?.`当对象不为空的时候正常调用方法，当为空的时候什么也不做。这里的作用就是为了避免空指针调用方法，你会说这好鸡肋啊没什么用，先别急后边会有更方便的方法一起组合来使用。

#### `?:`

我们先介绍第二个工具关键词：`?:`

这个操作符左右两边都接受一个表达式，如果左边表达式的结果不为空就返回左边的表达式，否则就返回右边的表达式的结果。你如果问这个有什么用？我们返回去看一段代码：

```kotlin
fun eat_empty_food (person: Person?) = 
	if (person != null) 
		person.eat_empty() 
	else 
		"empty"
```

`?:`就和if和else作用一摸一样，就是其的简写，这样看起来是不是很简单很方便？

```kotlin
fun eat_empty_food_plus (person: Person?) = person?.eat() ?: "empty"
```

当person为空的时候`?.`就会返回一个null这时候配合`?:`就实现了简化版的if else，从而避免了为null的情况。

#### let函数

let函数是kotlin独特的一个函数，这个函数接收的参数是一个函数式API接口，也就是需要你传入进去一个lambda表达式来使用，如果你不懂就翻回去看上一篇。

我这里直接介绍let如何使用：

```kotlin
obj.let{
	obj -> //函数体
}
```

举个例子：

```kotlin
private var special : String? = "empty_special"

//let函数
this.special?.let{it.length}

输出结果：
13
```

你可以发现let函数默认将调用它的对象作为参数传入到了lambda表达式中，这有什么用呢？

如果存在一种情况，你需要判断的对象调用了两个方法，你要这样写吗？

```kotlin
fun eat_twofood (person: Person?) = 
{
    person?.eat_empty()
    person?.eat()
}
```

这样一点也不优雅，打咩！

kotlin就是讲究优雅，我们改写以后使用let函数会将对象自动作为参数的特性：

```kotlin
fun eat_twofood (person: Person?) = person?.let { it.eat_empty(); it.eat();  }
```

kotlin的函数一行就足够了！

可以看到我们使用let函数的特性来实现了对单个对象引用多方法的判空处理，这样我们只需要判断一次就可以了，如果有多个方法调用，也只需要一次，省力又省时。我们只需要在let函数前使用`?.`判断一次即可,然后使用let函数的参数来调用对应方法。

### Kotlin判空的特殊情况

Kotlin的判空并不是完美的，某些情况下会阻碍我们的书写，因此要提前预知从而避免。

```kotlin
//特殊情况
if (test.special!=null)
    println(test.eat_empty_food_special())
```

其中`eat_empty_food_specia`方法如下：

```kotlin
fun eat_empty_food_special () = this.special.length
```

![image-20220408232756812](https://s2.loli.net/2024/09/29/lkxcVzMjhs3oUGq.png)

实际上这里是有报错的，因为这里编译器判定special可能为空，但是实际我们在函数的外，调用方法之前已经对其做了防空处理，但是IDE感知不到，但是问题不大，我们更换写法即可。





















