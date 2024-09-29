---
title:  Android开发笔记——快速入门（Kotlin的对象扩展与高阶函数深入）
categories:
  -  Android
  -  Kotlin
tags:
  -  Android
  -  Kotlin
excerpt: "Kotlin的对象扩展与高阶函数深入"
---

# Android开发笔记——快速入门 (Kotlin的对象扩展与高阶函数深入)



<img src="https://s2.loli.net/2024/09/29/RU7cAxv8aTSKQf4.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727618114855" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]



## Kotlin的对象扩展语法

### 对象表达式

有时候，我们需要创建一个对某个类有轻微改动的类的实例化对象，就要再去继承原来的类声明一个子类，但是通过Kotlin的对象表达式就可以避免去创建一个只使用一次的类。

#### 对象表达式

要创建一个继承自某个类的匿名类对象，我们可以使用对象表达式来简化书写，通过对象表达式将**匿名类**的声明和**匿名类对象**的声明糅合在一起，为我们省去了很多代码。

通常来说我们在使用接口作为参数的的时候使用对象表达式的情况比较多，举个例子，如果我们使用Thread来创建一个线程的时候，Thread类的参数实际上就是一个Ruunable接口，这个Runnable接口就是Java中所说的函数式接口，如果我们不使用lambda来传递的话，就要使用一个实现了**Ruunable接口类的对象**作为参数传递进去。或者使用匿名类的实现（实际上匿名类的实现也是对象）来替代需要实现的类的对象。

对于对象表达式就类似于的java的匿名类，只不过他的功能更多，在这里你可以先理解为Java中匿名内部类。

举个例子：

```kotlin
Thread(
    object : Runnable {
        override fun run() {
            println("Kotlin is ok with Anonymous")
        }
    }
).run()
```

这里我们创建了一个对象表达式然后继承了Runnable接口，并实现了其中的run方法，最终对象表达式会自动创建一个这个类的对象并返回给Thread类。

代码运行结果如下：

```shell
Kotlin is ok with Anonymous

Process finished with exit code 0
```

当然对象表达式的作用不仅仅局限于函数式接口，它可以用于任何可继承的类，下面再来讨论几种特殊情况。

##### 父类的构造函数存在参数

我们有一个父类如下：

```kotlin
package com.example.codewithkotlin

open class Person(var age :Int, val name:String)
{
    fun study() = name + "love study"
}
```

父类的形式很简单，就是只有一个方法，两个成员变量。

我们存在一个函数，需要传入一个父类的对象，就可以使用对象表达式来构造。

函数如下：

```kotlin
fun fatherargs_tester(person: Person)
{
    println(person.name)
}
```

解决办法如下：

```kotlin
/*父类的构造函数存在参数*/
fatherargs_tester(
    object :Person(18,"Jszszzy") {
    override fun study() = "$name love studing kotlin"
    }
)
```

可以看到我们使用对象表达式创建了一个新的对象，并且将类里面的方法进行了重写，输出是结果如下：

```shell
Jszszzy love studing kotlin
```

同时表达式可以访问其所在作用域的变量：

```kotlin
/*父类的构造函数存在参数*/
val name = "Jszszzy"

fatherargs_tester(
    object :Person(18,name) {
    override fun study() = "$name love studing kotlin"
    }
)
```

##### 匿名对象

对象表达式同时也可以构造**匿名对象**，即不继承任何类的匿名表达式。

举个例子：

```kotlin
val anonymous_object = object {
    val x = "test";
}
println(anonymous_object::class.java.toString())
```

我们使用：：class方法来获取到对应的类是什么。

结果如下:

```kotlin
class com.example.codewithkotlin.TestKt$main$anonymous_object$1
```

看以看到是一个特殊的类，类的名称和创建的变量名有一定关系。

##### 匿名对象作为返回值

这个是用的比较少，简单来说一下。

当匿名对象最为返回值的时候，

如果是一般的共有方法则返回类型自动转化为Any。

如果是私有办法则为返回类型为对应的匿名对象。

也就是说匿名对象，实际上其作用域仅限于他所处的类本身。

## Kotlin的类扩展

在某些情况下，我们希望在不修改某些类的源码的情况下（实际上你根本修改不了，大多数库代码都只是可读的）仍然可以为这个类添加新的功能，添加新的方法。就要使用到类的扩展了。

其基本使用结构如下：

```kotlin
fun Classname.methodName(param1:type,param2:type):Int{
	return 0
}
```

可以看到实际上就是定义了一个函数，在前面加上想要添加的具体类名就可以了。注意的是你在在哪里声明的添加方法就会收到其作用域的影响，所以往往把添加的方法直接放到文件中，拥有顶层作用域来使用。

## Kotlin的高阶函数

高阶函数和Lambda的关系密不可分，可以说高阶函数就是lambda应用的扩展。

高阶函数的定义是如果某个函数接收另一个函数作为参数或者返回值是一个函数就可以称之为高阶函数。你可能很难理解什么是函数类型？

实际上新一代语言中不止包含有我们常说的数据类型，大部分语言为了支持函数式编程，都添加了函数类型作为一种基础类型，他和其他基本类型一样，只不过其他基本类型传递的是一段二进制数据，函数类型传递的是一段可执行的代码。

对于一个lambda表达式来说，我们定义它其实很方便，举个例子，我们这里定义一个函数型变量（其实就是lambda表达式），并调用它输出它运行的结果：

```kotlin
/*Kotlin的类扩展*/
val lambda = { x:Int -> x*x }
println(lambda(3))
```

我们其实也知道kotlin有优秀的推导机制，其实上一个Lambda在声明的时候并没有指明具体返回类型，返回类型是在编译的时候推理出来的。

但是有些时候，Kotlin无法推断具体类型的情况下，就要手动来声明具体类型来指定了，声明类型也很简单，具体方法如下：

```kotlin
/*Kotlin的lambda使用*/
val lambda : (Int) -> Int =  {x -> x*x }
	println(lambda(3))
```

此处指明了lambda表达式的参数类型是Int、并且返回值类型依然是Int，最终输出的结果和上文一样。

说了这么多，这些又和高阶函数什么关系呢？

高阶函数的定义指明了要传递函数作为参数，那么如何定义函数参数呢？

没错就是lambda表达式来作为参数。

下面举一个例子，以下定义了一个高阶函数。

```kotlin
fun advanced_function( func :(Int) -> Int)
{
    println(func(9))
}
```

此高阶函数中参数为func，传入的lambda表达式类型为`(Int) -> Int`也就是说，接收一个输入参数为Int返回值Int的函数。

但是为什么要传入一个函数作为参数呢？发挥一下你的想象力，我们可以定义符合上述参数类型的lambda表达式，但是函数的逻辑内容可以完全不一样。举个例子：

我们仍然使用上述高阶函数，但是创建一个新的lambda表达式：

```kotlin
/*Kotlin的高阶函数*/
val lambda1 :(Int) ->Int  = {x -> x+x}
advanced_function(lambda1)
```

此时运算结果就变为了相加。

与此相对的，Kotlin也支持直接将lambda表达式传递进去，当高阶函数只有一个lambda参数的时候，我们可以直接省略小括号，最终效果和前边完全相同：

```kotlin
/*高阶函数常规用法*/
advanced_function{x -> x+x}
```

## 类扩展函数和高阶函数结合

当我们在前边使用apply函数的时候很方便就能获得一个上下文，这样调用对象的时候就可以避免重复书写对象，Kotlin同时也为高阶函数提供了类似的功能，我们先来尝试使用扩展函数和高阶函数结合一下：

```kotlin
fun StringBuilder.build(block : () -> Unit):StringBuilder
{
    block()
    return  this
}
```

这一段函数所代表的意义就是为StringBuilder类扩展一个高阶函数build，其参数为:`（）->Unit`的函数。

在函数内调用传入的函数，并将处理后的对象返回。

但是这里就会遇到一个问题，我如果想在传入的函数中使用StringBuilder的方法怎么办？也就说这部分函数还是要调用扩展类的方法的，Kotlin为此设计了一个能提供上下文的办法，使用起来很简单效果如下：

```kotlin
fun StringBuilder.build(block :  StringBuilder. () -> Unit):StringBuilder
{
    block()
    return  this
}
```

可以看到实际上就是在参数类型前边加上了ClassName.的前缀，这样就将要传入的函数参数定义在了在StringBuilder的里面，保证了函数可以自动获取StringBuilder的上下文。

举个实际例子：

```kotlin
val result = StringBuilder().build {
    append("jszszzy")
}
```

我们在传入的参数中加入了StringBuilder的上下文这样，就相当于获得了调用方法的时候创建的上下文，完整写法如下：

```kotlin
val result = StringBuilder().build {
    this.append("jszszzy")
}
```

最终结果也很简单，输出了添加的字符串：

```shell
jszszzy
```

## 深入理解高阶函数

### 内联函数

我们始终要意识到一个问题，kotlin的代码最终都要被编译为java的字节码文件的，但是在java中我们并没有高阶函数的概念，那么Kotlin是如何实现的高阶函数呢？

这就要归功于Koltin的强大编译器了，在这里我还是要狠狠吹一波Kotlin！Kotlin可不仅仅适用于JVM运行，他甚至还有专门的编译器去编译成类似c语言的字节码直接运行，不依赖于JVM,详细请了解：[Kotlin多平台](https://book.kotlincn.net/text/multiplatform.html)。

那么现在需要编译成JVM的字节码，他会怎么编译呢？我们还是拿上边的函数举个例子：

我们打开他的反编译java代码：

```java
public static final void advanced_function(@NotNull Function1 func) {
   Intrinsics.checkNotNullParameter(func, "func");
   int var1 = ((Number)func.invoke(9)).intValue();
   System.out.println(var1);
}
```

可以看到我们的函数类型参数转变为了一个Function1接口，这是神马？

我们打开看一看：

```kotlin
/** A function that takes 1 argument. */
public interface Function1<in P1, out R> : Function<R> {
    /** Invokes the function with the specified argument. */
    public operator fun invoke(p1: P1): R
}
```

里面有一个函数叫invoke，继承这个接口的话，那必须就要把这个函数给实现了。

可以看到原来我们执行传入的函数参数的位置被替换成了一行代码：

```kotlin
 int var1 = ((Number)func.invoke(9)).intValue();
```

原来的函数的调用被替换为了一个invoke()函数的调用。

实际上在调用高阶函数的时候，对应的代码被转换为了如下的样子：

```kotlin
/*替换前源代码*/
advanced_function{x -> x+x}

/*替换后*/
advanced_function(
    new Function(){
        @Overrride
        public Integer invoke(Integer x){
            retaurn x+x;
        }
    }
)
```

也就是说对应的lambda表达式位置被替换成了一个匿名类的实现，通过这个接口将对应的lambda表达式封装到了invoke函数里面，调用invoke函数就相当于使用了对应的lambda表达式！

但是这就产生了一个问题！我的每一个lambda不会都是这样实现的吧，这样就会造成很大的性能浪费，当你在上边愉快的书写lambda表达式，下边kotlin跑的匿名类累死。

kotlin为了消除这个问题，又创建了一个方法，这就是内联函数，内联函数是干什么的呢？

他会使Kotlin编译器将内联函数中的代码在编译的时候替换到对应的地方。实现真正的代码传递。这样在运行的时候就不存在任何运行开销了。说起来可能不太容易，我们做个演示，替换后调用的位置是什么样的:

使用inline来修饰：

```kotlin
inline fun advanced_function( func :(Int) -> Int)
{
    println(func(9))
}
```

对应调用位置的代码：

```kotlin
/*使用内联函数替代后*/
println(x+x)
```

你会发现他把原来你调用的函数给替掉了，实际上就是把lambda表达式融合进函数里面最终在再把函数内所有的代码都拿出来替换掉调用的地方。

当然他的替换过程可能没有那么简单，因为在内联函数的内部可能存在return等语句所以我们在这里不再详细介绍具体的实现过程，我们不妨来说一说一个便捷的理解方法。

- Kotlin编译器首先将Lambda表达式中的代码替换到内联函数调用函数型参数的地方。
- 接下来，再将内联函数中的全部代码再反向替换到内联函数调用的地方
- 左后将内联函数中的return等操作省略，将return的表达式或者值直接省略替换成对应的表达式。

### noinline和crossinline

接下来我们再来讨论一下，使用inline关键字的内联函数特殊情况。

在某些情况下你需要打断lambda表达式的运行，就是需要在lambda表达式中使用return，这时候就麻烦了lambda表达式中一般是不允许使用return的，你当然也可以使用特殊的关键字`return@内联函数名称`，使用以后lambda表达式剩余的内容就不会执行。

举个例子：

```kotlin
/*Kotlin 内联函数*/
advanced_function{
        x ->
    return@advanced_function x*x
    x+x
}
```

这里运行的结果就是返回一个x*x.而不是下面的x+x。

那我如果使用inline关键字呢？

回想上文所说的，inline关键字实际上是代码替代，所以可以直接使用return来返回，不过注意的是这里return会导致调用函数直接return！

这就是高阶函数使用inline关键字的第一个关注点。

第二个关键点在于高阶函数内部的lambda表达式需要使用函数型参数作为lambda表达式的内容会因为return关键字产生冲突。

看起来很复杂实际上是一种嵌套的情况，我们来举个例子：

![image-20220617175447968](https://s2.loli.net/2024/09/29/OAh7siKrDbE3MN5.png)

可以看到这里报错了，这里的代码实际上在匿名类中调用了传入的函数类型参数。

因为使用了inline，所以可能会导致Runnable接口的lambda中包含了全局返回使用的return。

但是我们如果lambda中其实并没有包含return呢？有什么办法绕开这个错误呢？

当然！

```kotlin
inline fun advanced_function_withlambda( crossinline func :(Int) -> Int)
{
    val runnable = Runnable {
        func(9)
    }
}
```

crossinline本质上解决的问题是在声明了inline关键字的高阶函数内部的lambda表达式需要使用函数型参数作为lambda表达式的内容而产生的冲突。再本质一点的问题是，lambda表达式和匿名类一样只允许函数局部返回，而对于内联操作使用的确实为全局返回。

总的来说如果你的高阶函数的lambda表达式使用了return关键字，那么你就应该考虑到以上的关键字和return带来的具体影响。





























