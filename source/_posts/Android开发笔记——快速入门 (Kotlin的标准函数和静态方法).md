---
title:  Android开发笔记——快速入门（Kotlin的标准函数和静态方法）
categories:
  -  Android
tags:
  - Android
  - Kotlin
excerpt: "Kotlin的延迟初始化与Viewbinding、密封类"
---

# Android开发笔记——快速入门（Kotlin的标准函数和静态方法）

<img src="https://s2.loli.net/2024/10/04/Ash97EO3iRUGNo4.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727980663756" style="zoom:50%;" />



<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]



## Kotlin的标准函数和静态方法

Kotlin的标准函数指的是Standard.kt文件中定义的函数，任何Kotlin代码都可以自由调用所有标准函数

比如上一次所说的Let函数就是标准函数之一。

### Kotlin的with函数

with函数的参数：

- 第一个为任意类型的对象
- 第二个是Lambda表达式

with函数的作用：

- 在lambda表达式中提供第一个参数作为对应的上下文
- 使用表达式中最后一行代码的返回值作为返回值返回

那么这个函数具体有什么作用呢？看一下示例代码：

我们通过**Stringbuilder**对象来实现将ArrayList对象中保存的People实例转化成一个同一的字符串

![1](https://s2.loli.net/2024/10/04/1dlNVDtcP3SnJp9.png)

```kotlin
fun withFunTester()
{
    val string = StringBuilder()
    val list_people = arrayListOf(Person(19,"jszszzy"), Person(20,"jszslxl"))

    with(string){
        for ( people in list_people)
        {
            append(people).append("\n")
        }
        val result =string.toString()
        println(result)
    }
}
```

可以看到我们为了更简介的去构造这个字符串通过with函数将构造字符串的对象作为上下文来引用这样就省去了多次调用Stringbuilder的对象的麻烦。

调用结果如下：

![image-20220429233250384](https://s2.loli.net/2024/10/04/F7zLdsUaeEBvJRc.png)

因为People类是自定义的类并没有直接重写tostring方法所以返回的是对象的的具体信息

### Kotlin的run函数

run函数的参数：

- 第一个就是Lambda表达式
- 需要对象直接调用此函数

run函数的作用：

- 在lambda表达式把调用对象作为对应的上下文
- 使用lambda表达式中最后一行代码的返回值作为返回值返回

我们改写一下上一个函数使用run函数来实现：

```kotlin
fun runFunTester()
{
    val string = StringBuilder()
    val list_people = arrayListOf(Person(19,"jszszzy"), Person(20,"jszslxl"))
    val runFunTester= string.run {
        for ( people in list_people)
        {
            append(people).append("\n")
        }
        string.toString()
    }
    println(runFunTester)
}
```

输出结果和上一个相同，这里不再赘述。

### Kotlin的apply函数

apply函数的参数：

- 第一个就是Lambda表达式
- 需要对象直接调用此函数

apply函数的作用：

- 在lambda表达式把调用对象作为对应的上下文
- apply无法指定返回值而将调用对象作为返回值返回

举个例子：

可以看到实际上函数里面并没有写任何作为返回值的表达式，其返回的正是Stringbuilder的对象，只不过println自动调用的是对象的tostring方法。

```kotlin
fun applyFunTester()
{
    val string = StringBuilder()
    val list_people = arrayListOf(Person(19,"jszszzy"), Person(20,"jszslxl"))
    val applyFunTester= string.apply {
        for ( people in list_people)
        {
            append(people).append("\n")
        }
    }
    println(applyFunTester)
}
```

### Koltin的静态方法

Kotlin的设计者一直在弱化静态方法这个概念，为了什么呢？因为从本质上来说Kotlin已经提供了更方便的方法来实现类似于静态方法的操作。

举个例子：

Kotlin提供了一个单例类作为静态方法的实现办法。

我们创建一个单例类，实际上JVM已经替我们创建好了一个对象，找个对象不需要你自己手动创建，而是帮你创建好了和类名相同的对象，直接

`类名.方法`即可实现方法调用。

这里我们创建一个Utils类，把刚才使用的函数都放入Util类中来进行调用。

```kotlin
object Utils
{
    fun withFunTester()
    {
        val string = StringBuilder()
        val list_people = arrayListOf(Person(19,"jszszzy"), Person(20,"jszslxl"))
        val withFunTester=with(string){
            for ( people in list_people)
            {
                append(people).append("\n")
            }
            val result =string.toString()
            println(result)
        }
    }
    fun runFunTester()
    {
        val string = StringBuilder()
        val list_people = arrayListOf(Person(19,"jszszzy"), Person(20,"jszslxl"))
        val runFunTester= string.run {
            for ( people in list_people)
            {
                append(people).append("\n")
            }
            string.toString()
        }
        println(runFunTester)
    }
    fun applyFunTester()
    {
        val string = StringBuilder()
        val list_people = arrayListOf(Person(19,"jszszzy"), Person(20,"jszslxl"))
        val applyFunTester= string.apply {
            for ( people in list_people)
            {
                append(people).append("\n")
            }
        }
        println(applyFunTester)
    }
}
```

在main函数中直接调用即可：

```kotlin
fun main(args: Array<String>)
{
	Utils.runFunTester()
}
```

当然你会注意到有个问题，在Utils类中所有方法都是静态的，那如果我想部分函数是静态的怎么办呢？

Kotlin也提供了一个方法,**伴生对象**。

通过伴生对象就可以实现在一个类中既有静态也有一般方法。

我们将Utills类改为一般的类，然后使用伴生对象来实现静态和一般调用。

```kotlin
class Utils
{
    fun withFunTester()
    {
        val string = StringBuilder()
        val list_people = arrayListOf(Person(19,"jszszzy"), Person(20,"jszslxl"))
        val withFunTester=with(string){
            for ( people in list_people)
            {
                append(people).append("\n")
            }
            val result =string.toString()
            println(result)
        }
    }
    
    companion object
    {
        fun runFunTester()
        {
            val string = StringBuilder()
            val list_people = arrayListOf(Person(19,"jszszzy"), Person(20,"jszslxl"))
            val runFunTester= string.run {
                for ( people in list_people)
                {
                    append(people).append("\n")
                }
                string.toString()
            }
            println(runFunTester)
        }
        fun applyFunTester()
        {
            val string = StringBuilder()
            val list_people = arrayListOf(Person(19,"jszszzy"), Person(20,"jszslxl"))
            val applyFunTester= string.apply {
                for ( people in list_people)
                {
                    append(people).append("\n")
                }
            }
            println(applyFunTester)
        }
    }
}
```

伴生对象的使用很简单，直接在普通的类中这样即可：

```kotlin
class objext1
{
	companion object
    {
    
    }
}
```

`companion object`大括号中的内容就是伴生对象的**‘’静态方法‘’**,但需要注意的是实际上这两种方法都不是真正的静态方法，而是通过系统创建的同名对象实现的。

#### Koltin的真正静态方法

Kotlin同样提供了两种方法，来实现真正的静态方法。对于上边两种假的静态方法，如果你的代码需要在Java里面以静态的方法去调用，你会发现这些方法实际上根本不存在。而如果我们需要真正的静态方法呢？

##### `@JvmStatic`注解

`@JvmStatic`注解只能作用在单例类或者伴生类中的方法上，如果你尝试在普通类的方法是使用这个注解就会产生报错。

举个例子：

```kotlin
companion object
{

    @JvmStatic
    fun applyFunTester()
    {
        val string = StringBuilder()
        val list_people = arrayListOf(Person(19,"jszszzy"), Person(20,"jszslxl"))
        val applyFunTester= string.apply {
            for ( people in list_people)
            {
                append(people).append("\n")
            }
        }
        println(applyFunTester)
    }
}
```

那么如何调用呢？

调用的方式也很简单，就是静态方法的调用模式：

```Kotlin
fun main(args: Array<String>)
{
    Utils.runFunTester()
    Utils.applyFunTester()
}
```

可以看到上边两种方法虽然在Kotlin中看起来一摸一样实际上原理并不相同。

##### 顶层方法

我们在前几张说过main函数有些特殊他是直接写在一个文件里面的，里面并没有包含任何类，就像这样：

![image-20220502094322826](https://s2.loli.net/2024/10/04/QYeV3D6cK2OoiXm.png)

Kotlin的编译器会将所有顶层方法全部编译成静态方法，因此只要你定义了一个顶层方法，那么他一定是静态方法。在前几节我们使用test类作为单例类来创建静态的main方法，这里我们抛弃多余的test类，然后使用顶层方法作为mian方法让JVM调用，效果如上图所示。

那么对于顶层方法如何调用呢？

我们再将`withFunTester`方法作为顶层方法来看看如何使用：

创建一个Kotlin File后直接把方法放进去即可，效果如下：

![image-20220502095102381](https://s2.loli.net/2024/10/04/3QR6kJuKiEzcU1j.png)

我们在main函数直接调用即可！对!不需要管路径，也不用创建实例，直接键入方法名即可：

![image-20220502095418638](https://s2.loli.net/2024/10/04/OAltvENwy5pfSJh.png)

当然这里还是要提一嘴，如果要在java中调用Kotlin编写的静态方法怎么办？

我们创建的文件名为：Statictester.kt

当编译完成的时候，Kotlin的编译器实际上创建了一个Statictesterkt类，静态方法就按照java的静态形式定义在类里面，所以按照文件名.方法的形式来调用就行了。







































