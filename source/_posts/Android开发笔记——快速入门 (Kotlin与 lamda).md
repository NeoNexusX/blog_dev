---
title:  Android开发笔记——快速入门（Kotlin与lambda）
categories:
  -  Android
  -  Kotlin
tags:
  -  Android
  -  Kotlin
excerpt: "Kotlin入门；Kotlin与lambda，list set map 闭包"
---

# Android开发笔记——快速入门（Kotlin与lambda）



<img src="https://s2.loli.net/2024/09/29/Y1eySEM3hX46p8C.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727617420599" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />



#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]



## Kotlin与lambda

### Kotlin的集合

在介绍lambda表达式之前先介绍一下kotlin中的集合类，传统意义上的集合主要是list和set还有map。下面来一一介绍一下kotlin中这些内容如何使用。

#### kotlin的list

**list的主要实现类是ArrayList与LinkedList**，这里主要是介绍**List**而不是它的具体实现类

List的特性很简单：

- 允许出现重复的元素。
- 元素有序，存入和取出的顺序一致。
- 元素以一种线性的方式存储，在程序中可以通过索引来访问集合中的主要元素。

使用传统的list像java一样通过函数一个一个初始化添加在kotlin中也是可以的：

```kotlin
val list_test = ArrayList<String>()
list_test.add("apple")
list_test.add("pear")
```

不过kotlin提供了更加方便的方法,我们使用`listof`来实现更加方便快捷:

```kotlin
val list_kotlin = listOf<String>("apple","pear")
for (list in list_kotlin)
println(list)
```

你可以注意到这里声明的list使用的是val关键字而不是var说明他是一个不可变的量，除了初始化添加的内容不允许再添加其他内容。

输出结果如下

```shell
apple
pear
```

#### kotlin可变的list

kotlin中使用可修改的list也很简单，更换关键字为`mutableListOf`即可：

```kotlin
var list_var = mutableListOf("apple","pear")
list_var.add("no fruits")
println(list_var[0])
println(list_var[2])
```

可以看到调用了add来进行添加。

输出结果：

```kotlin
apple
no fruits
```

##### kotlin的ArrayList

List与ArrayList的不同点：

List实际上是接口并不是一个普通的类

ArrayList继承了几乎全部的**MutableList**的方法，除了继承的方法ArrayList类内部还实现了两个独特的方法：

```kotlin
fun trimToSize()
fun ensureCapacity(minCapacity: Int)
```

分别是可以扩容和缩小占用内存，具体可以见：[ArrayList的trimToSize](https://blog.csdn.net/gzheclipse/article/details/105956271)。

但是！你如果使用**mutableListOf**来实现一个可变的列表，它实际上返回的就是**ArrayList！**

不信可以看源码：

```kotlin
/**
 * Returns a new [MutableList] with the given elements.
 * @sample samples.collections.Collections.Lists.mutableList
 */
public fun <T> mutableListOf(vararg elements: T): MutableList<T> =
    if (elements.size == 0) ArrayList() else ArrayList(ArrayAsCollection(elements, isVarargs = true))
```

ArrayList是List接口的一个实现类，它是程序中最常见的一种集合。

```kotlin
val list_kotlin = arrayListOf("apple","pear")
for (list in list_kotlin)
    println(list)
```

它的使用和list基本一致，当然效率相较于list可能会有些下降，因为ArrayList集合在增加或删除指定位置的数据时，会创建新的数组导致效率变低。

#### kotlin的set

set类与list的不同在于底层，set不允许集合内存在相同的内容，set的集合底层使用的是hash映射机制来存放数据的，因此集合的元素保证了内容的不重复，但同时也失去了顺序。

set的演示：

```kotlin
//set 演示
val set_kotlin = setOf("apple","pear","apple")
for (set in set_kotlin)
    println(set)
```

可变set演示：

```kotlin
var set_var = mutableSetOf("1","2","3","4")
for (set in set_var)
    println(set)
```

#### kotlin的map

map类就不再多说，实际上就是通过键值和内容实现的映射集合，值得注意的是他的迭代方式有所不同：

```kotlin
val map_test = HashMap<String,Int>()
map_test["apple"] = 1
map_test["banana"] = 2
for ((fruit ,number) in map_test)
    println("fruit is $fruit number is $number")
```

前面介绍过for循环转变为了迭代器，这里就充分发挥了其迭代器的特性，可以同时迭代map中的内容和键值。可以看到输出结果：

```kotlin
fruit is banana number is 2
fruit is apple number is 1
```

当然kotlin也提供了简洁的写法通过`to`来实现，键值与内容的映射，但to在这里并不是关键字而实更复杂的内容，我们到后边再说：

```kotlin
val map_kotlin = mapOf("apple" to 1,"banana" to 2)
for ((fruit ,number) in map_kotlin)
    println("fruit is $fruit number is $number")
```

输出结果：

```kotlin
fruit is apple number is 1
fruit is banana number is 2
```

可以看到两次输出结果并不一致，这里先挖一个坑，当然你也注意到set并不能直接用【】类似于数组下标的方式访问，这些等以后再详细说明。

### kotlin的lambda

集合的函数式API作为lambda表达式的演示再好不过了。

首先我们来说一说什么是lambda。

**Lambda就是一小段可以作为参数传递的代码**，这就很厉害，因为常规情况下我们作为参数传递的都是变量，而参数转变为lambda表达式的时候就可以传递进去一段带有逻辑处理的代码。

对于一小段这个量词的定义并不明确，通产来说在能保持可读性的前提下可以尽量的短或长。

lambda的常规语法：

```
{参数名1 ：参数类型，参数名2，参数类型 -> 函数体（代码块）}
```

首先最外层是一个大括号，如果有参数需要向lambda传递的话我们需要先声明参数列表，参数列表的结尾是一个“->”这代表着参数的结束和函数体的开始，**函数体的最后一行会自动作为lambda表达式的返回值**。

#### 什么时候需要传递lambda作为参数？

在Java里面lambda表达式出现所要替代的对象实际上Java的**单抽像方法接口**也可以被叫做**函数式接口**，他俩共同的所代表的意义就是：

**接口中有且仅有一个抽象方法需要被实现**。

**换句话说就是接口里面只有一个需要被实现的方法。**

当**这种类型的接口**作为参数的时候，我们就需要传递一个内部匿名类来实现其抽象方法

这里为了方便下边讲解先说几个结论，并不完全，可自行推广：

- kotlin的函数式API
- kotlin的函数式接口
- Java的函数式接口作为参数

#### kotlin的闭包

与其说Kotlin是一等公民，不如说是闭包才是一等公民。

闭包在kotlin中常常指的就是由{}构成的lambda表达式，之所以叫他闭包，实际上就是因为他与函数有很大的区别，比如闭包可以访问外部环境的变量，普通函数想要访问外部环境的变量是需要传入参数的，而闭包可以直接访问，并将其保存下来，具体可见参考文章。

参考文章：

https://www.jianshu.com/p/b968524a0e95。

[闭包2](https://kymjs.com/code/2017/04/09/01/#:~:text=%E9%97%AD%E5%8C%85%E5%BA%94%E8%AF%A5%E7%AE%97%E6%98%AF%20Kotlin%20%E6%9C%80%E6%A0%B8%E5%BF%83%E7%89%B9%E6%80%A7%E4%B9%8B%E4%B8%80%E4%BA%86%E3%80%82%20%E4%BD%BF%E7%94%A8%E5%A5%BD%E9%97%AD%E5%8C%85%E5%8F%AF%E4%BB%A5%E8%AE%A9%E4%BB%A3%E7%A0%81%E9%87%8F%E5%A4%A7%E5%A4%A7%E5%87%8F%E5%B0%91%EF%BC%8C%E4%BE%8B%E5%A6%82%20Kotlin%20%E6%9C%80%E8%91%97%E5%90%8D%E7%9A%84%E5%BC%80%E6%BA%90%E5%BA%93%EF%BC%9A%20Anko,%EF%BC%8C%E4%BD%BF%E7%94%A8%20Anko%20%E5%8E%BB%E5%8A%A8%E6%80%81%E4%BB%A3%E7%A0%81%E5%B8%83%E5%B1%80%EF%BC%8C%E6%AF%94%E4%BD%BF%E7%94%A8%20Java%20%E4%BB%A3%E7%A0%81%E9%85%8D%E5%90%88%20xml%20%E8%A6%81%E6%9B%B4%E5%8A%A0%E7%AE%80%E6%B4%81%E3%80%82%20%E5%8F%AF%E4%BB%A5%E7%9C%8B%E5%88%B0%EF%BC%8C%E5%85%85%E5%88%86%E8%BF%90%E7%94%A8%E4%BA%86%E9%97%AD%E5%8C%85%E7%9A%84%E7%81%B5%E6%B4%BB%E6%80%A7%EF%BC%8C%E7%9C%81%E7%95%A5%E4%BA%86%E5%BE%88%E5%A4%9A%E7%9A%84%E4%B8%B4%E6%97%B6%E5%8F%98%E9%87%8F%E5%92%8C%E5%8F%82%E6%95%B0%E5%A3%B0%E6%98%8E%E3%80%82).

我在这里举一个例子方便大家理解一下，如果看不懂看完本文再回过头来看效果更好：

```kotlin
    val list = listOf("Apple", "Banana", "Orange", "Pear",
        "Grape", "Watermelon")
    val newList = list.map() { fruit: String ->
        {fruit.uppercase() }}

    for (fruit in newList) {
        println(fruit)
    }
```

这里实际上输出的是闭包或者说是函数类型常量。

##### kotlin函数式API

我们先说明什么是kotlin函数式API：

先看一段代码，在最开始的一行我们定义了一个lambda表达式，他接受一个String类型的参数并返回字符的长度。

```kotlin
//lambda 表达式演示
val lambda = { fruit:String -> fruit.length}
val Max = list_test.maxOf(lambda)
println(Max)
```

我们跳转到maxof函数的定义去看看一看它的参数写的是什么：

```kotlin
public inline fun <T, R : Comparable<R>> Iterable<T>.maxOf(selector: (T) -> R): R {
    val iterator = iterator()
    if (!iterator.hasNext()) throw NoSuchElementException()
    var maxValue = selector(iterator.next())
    while (iterator.hasNext()) {
        val v = selector(iterator.next())
        if (maxValue < v) {
            maxValue = v
        }
    }
    return maxValue
}
```

可以看到它的参数实际上就是一个lambda表达式：`selector: (T) -> R`

是不是很简单？很明了？我们再来看看什么是kotlin的函数式接口：

##### kotlin的函数式接口

在kotlin1.4中更新了函数接口，与java不同的是，kotlin需要显示的来定义一个函数接口，在`interface`接口添加一个`fun`关键字就可以实现把他转换为函数式接口：

```kotlin
fun interface Eat
{
    fun eat_rice()
}
```

当函数式接口作为参数的时候就可以使用kotlin的lamda表达式：

```kotlin
//lambda
val lambda2 = { -> println("lambda is ok with kotlin interface")}
test.lambda_tester(lambda2)
```

因为原方法中并没有参数，所以前边也没有任何参数需要传递，我们可以简写成：

```kotlin
//lambda
val lambda2 = { println("lambda is ok with kotlin interface")}
test.lambda_tester(lambda2)
```

##### Java的函数式接口作为参数

如果我们在Kotlin代码中调用一个java方法，并且该方法只接收一个java单抽像方法接口作为参数，我们就可以使用lambda来传递参数：

我们这里来拿java的Thread类来举例子：

thread类的构造方法接收一个Runnable接口作为参数，Runnable是典型的函数式接口，里面只有一个run方法需要实现，当我们使用匿名内部类来实现的时候写法如下：

```kotlin
Thread(object :Runnable
{
    override fun run() {
        println("kotlin is  ok with anonymous")
    }
}).start()
```

看起来很复杂，这里不再介绍匿名类的具体内容，你只需要知道 kotlin完全舍弃new关键字，这里的object类似于创建一个内部类，先尝试理解一下，实际上和这个感知还是有一定差距，到后面再说，我们直接来说如何用kotlin的lambda来实现java的函数式接口：

```kotlin
Thread(
    Runnable{-> println("kotlin is ok with java")}
		).start()

```

语法格式：

```shell
接口名 { 参数 -> 函数体 }
```

讲到这里我想你应该明白什么时候用lambda什么参数的情况下使用lambda。

接下来我们顺着kotlin的设计思想，来讨论一下他的语法糖，上边为了你方便理解我并没有使用简写，但kotlin的最关键的还是他的语法糖，语法糖很甜。

##### kotlin的语法糖

我们回到maxof方法：

```kotlin
//lambda 表达式演示
val lambda = { fruit:String -> fruit.length}
val Max = list_test.maxOf(lambda)
```

实际上这里的lambda参数可以改写为：

```kotlin
val Max = list_test.maxOf({ fruit:String -> fruit.length})
```

kotlin规定当lambda参数是函数的最后一个参数的时候可以放到括号外边：

```kotlin
val Max = list_test.maxOf(){ fruit:String -> fruit.length}
```

接下来如果lambda表达式时函数唯一的参数的话还可以将括号省略：

```kotlin
val Max = list_test.maxOf{ fruit:String -> fruit.length}
```

不要忘了kotlin具有很强的推导类型的能力，所以参数类型也可以省略：

```kotlin
val Max = list_test.maxOf{ fruit -> fruit.length}
```

最后如果lambda表达式中只有一个参数的时候可以直接省略参数使用it来替代，同时“->”也可以省略：

```kotlin
val Max = list_test.maxOf{ it.length}
```

你会对这些省略规则感到复杂，其实抓住重点就是几条：

- 最后一个参数 ->移出到外边
- 唯一参数 - > 省略和替代
- 参数类型-  >可推导的省略

我们按照这三个语法糖来实现代码优化：

##### Java函数式API的语法糖

```kotlin
Thread(object :Runnable
{
    override fun run() {
        println("kotlin is  ok with anonymous")
    }
}).start()
```

首先Runnable接口是一个java的函数式接口，也就是其中只有一个抽象方法，并且对于Tread来说构造函数也只有这样一个接口参数，这两个唯一就符合上边的省略的替代策略。

省略接口名称，和重写方法的名称：

```kotlin
Thread({
        println("kotlin is ok with java")
    }).start()
```

同时他也是最后一个参数，也是唯一的参数省略括号和外移：

```kotlin
Thread{
        println("kotlin is ok with java")
    }.start()
```

牢记语法糖的实现规则，很方便的来优化代码。

































































