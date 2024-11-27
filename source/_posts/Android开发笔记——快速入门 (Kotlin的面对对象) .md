---
title:  Android开发笔记——快速入门（Kotlin的面对对象）
categories:
  -  Android
tags:
  - Android
  - Kotlin
excerpt: "Kotlin的面对对象"
date: 2024/10/22 00:00:00
---

# Android开发笔记——快速入门（Kotlin的面对对象）

<img src="https://s2.loli.net/2024/10/04/TU4j1YWLER9caFp.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727980774295" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]



## Kotlin的面对对象

#### 类与对象

在上一节我们创建了一个类但是并没有使用，所有的代码都是通过函数实现的，这并不符合面向对象的编程的要求。至于什么是面向对象编程这里就不再多说了有兴趣的或不了解的可以去知乎看一看。

这里我们再新建一个class名叫Person。Kotlin 中使用关键字 `class` 声明类。

![1](https://s2.loli.net/2024/10/04/DF2Pz5Yp9vkL1ar.png)

类声明由类名、类头（指定其类型参数、主构造函数等）以及由花括号包围的类体构成。类头与类体都是可选的； 如果一个类没有类体，可以省略花括号。

```kotlin
class Person
```

这样也是完全正确的。

给他添加一些字段：

```Kotlin
package com.example.codewithkotlin

class Person
{
    var  age :Int =0 ;
    val name  = "";
}
```

可以看到我这里使用了两种写法来提示你字段也能自动推导类型。

下面我们来考虑一下如何实例化这个类：

```kotlin
package com.example.codewithkotlin

class Person
{
    var  age :Int =0 ;
    val name  = "";

}
fun main()
{
    var jack = Person()
    println(jack.age)
}
```

首先看到代码时你如果有Java的编程基础你会有疑问，main为什么不在类里面？而且也不是静态方法？这里暂且先放一放，我们到后边再来说这个我问题，这里先接受它，能运行，是一个程序的入口。

具体在里面呢我们实例化了一个类，你可以看看非常轻巧简单，保持着**能省就省**的原则，不需要类型名，不需要new关键字，直接调用类名+（）就实现了初始化：

```Kotlin
fun main()
{
    var jack = Person()
    println(jack.age)
}
```

#### Kotlin的方法

方法也没什么好说的，他和函数的语法糖一摸一样，连关键字也一摸一样，你可以用一行来写一个方法的简单明了，调用的时候也是实例+.+方法。例子如下：

```kotlin
package com.example.codewithkotlin

class Person
{
    var  age :Int =0 ;
    val name  = "";
    fun study() =  "zzy love study"
}
fun main()
{
    var zzy = Person()
    println(zzy.age)
    println(zzy.study())
}
```

输出结果如下：

![2](https://s2.loli.net/2024/10/04/NpirAk83Z72XQGm.png)

#### Kotlin的继承与构造

关于继承的概念也不再详细解释，感兴趣请移步知乎。

这里直接介绍用法和技巧.

为了验证使用我们这里需要新建一个类：

![3](https://s2.loli.net/2024/10/04/UmjrRzg1743pfxh.png)

继承的方法很简单，kotlin将关键字extends替换位：，写起来相对简单一些。可以看到如下就继承了Person类，同时也继承了其类内部的字段。

```kotlin
class Zzy : Person()
{

}
```

这里也许你会产生疑问，为啥后边还会跟一个（）？这不就是打破了能省就省的设计思想了吗？当然不是啦！

这个（）就是每个面向对象不可或缺的构造函数，只不过我们目前构造的都是无参的构造函数，在类后边就不需要写无参构造函数的括号。

在前边我们说过Kotlin的类的声明中可以省略类体和类头，在 Kotlin 中的一个类可以有一个*主构造函数*以及一个或多个*次构造函数*。主构造函数是类头的一部分：它跟在类名与可选的类型参数后。

```kotlin
class Person constructor() { /*……*/ }
```

如果主构造函数没有任何注解或者可见性修饰符，可以省略这个 `constructor` 关键字。

```kotlin
class Person() { /*……*/ }
```

如果构造函数有注解或可见性修饰符，这个 `constructor` 关键字是必需的，并且这些修饰符在它前面：

```kotlin
class Customer public @Inject constructor(name: String) { /*……*/ }
```

那么在没有修饰符的情况下，有参数的构造函数如何写呢？我们反过来修改一下父类：

```kotlin
open class Person(age :Int, name:String)
{
    fun study() = name + "love study"
}
```

可以看到需要在构造函数里声明的字段就写好了，在实例化的时候就需要传入这些字段的参数：

```kotlin
open class Person(age :Int, name:String)
{
    val name :String = name;
    var age :Int = age;
    fun study() = name + "love study"
}
```

这里值得注意的是，这里的age，name和下面字段所指并不是一个，构造函数的参数只是个构造函数的局部变量，而不是成员变量，你如果想直接声明成字段的话就要显示的指定他是val还是var，指定后默认将其作为字段来初始化。

```kotlin
open class Person(var age :Int, val name:String)
{
    fun study() = name + "love study"
}
```

你可能还会有疑问，那我想写一些逻辑处理函数怎么办呢？Kotlin同时也提供了构造函数的函数体：

```kotlin
open class Person(age :Int, name:String)
{
    val name :String = name;
    var age :Int = 0;//这里不初始化等到init再来初始化它。
    init {
        this.age = age;
    }
    fun study() = name + "love study"
}
```

我们再回到继承上来说，有参的父类怎么继承？很简单在对应的位置输入构造参数就行了：

```Kotlin
package com.example.codewithkotlin

class Zzy(gender: String) : Person(22,"zhuzeyu")
{

}
```

同样你也可以使用它的构造参数：

```kotlin
class Zzy(val gender: String) : Person(22,"zhuzeyu")
{
	init {
    		println(name+" is a "+gender+" age is "+age)
		}
}

//输出结果
zhuzeyuis a man age is 22
```

在我们讨论完了主构造函数以后，既然有主构造函数那么一定有次构造函数，来看看他如何使用：

```kotlin
class Zzy(val gender: String) : Person(22,"zhuzeyu")
{
    constructor(iq:Int, eq:Int) : this("man")
    {
        println("his iq is "+iq+" eq is " + eq)
    }

    init {
    println(name+" is a "+gender+" age is "+age)
    }

}
```

使用很简单，constructor关键字就是来创建构造函数的，kotlin规定次构造函数必须要调用主构造函数，所以你可以看到this关键字来调用主构造函数。与主构造函数不同的是此构造函数有函数体，并且次构造函数没办法直接声明成员，这里需要注意一下，参数的作用范围只有在此构造函数之中。

调用实例：

```kotlin
fun main()
{
    var zzy = Zzy(120,100)
}
输出结果：
zhuzeyu is a man age is 22
his iq is 120 eq is 100
```

#### Kotlin的接口与面向接口编程

kotlin和java一样都不是多继承的面向对象编程，也就是说你只能继承一个父类，但是对于子类来说需要多继承的时候就很麻烦了，所以这里选择接口会更方便。

接口和普通类的界限在逐渐变得模糊，不管是针对java还是kotlin都是一样，可以继承多个接口，接口的继承演示如下：

继承的关键字同样被`：`所替代，和继承的父类可以直接一起使用，将它们分开就可以同时继承。

```kotlin
class Zzy(val gender: String) : Person(22,"zhuzeyu"),Eat
{
    //实现了两个接口中的函数
    override fun drink_soap() {
        println("drink soap")
    }

    override fun eat_rice() {
       println("eat rice")
    }
    constructor(iq:Int, eq:Int) : this("man")
    {
        println("his iq is "+iq+" eq is " + eq)
    }

    init {
    println(name+" is a "+gender+" age is "+age)
    }

}
```

Eat接口：

```kotlin
interface Eat
{
    fun drink_soap()
    fun eat_rice()
}
```

为什么说接口和普通类正在变得模糊呢？我们再来看一个操作：

##### 在接口中预先实现一个方法（默认实现）

如果你在接口中实现了一个方法：

```kotlin
interface Eat
{
    fun drink_soap(){
        println("drink soap")
    }
    fun eat_rice()
}
```

在类的实例就可以直接不需要重写他们，IDEA也不会报错：

```kotlin
class Zzy(val gender: String) : Person(22,"zhuzeyu"),Eat
{
    override fun eat_rice() 
    {
       println("eat rice")
    }
    constructor(iq:Int, eq:Int) : this("man")
    {
        println("his iq is "+iq+" eq is " + eq)
    }
    init 
    {
        println(name+" is a "+gender+" age is "+age)
    }
}
```

在使用的时候直接调用即可：

```kotlin
fun main()
{
    var zzy = Zzy(120,100)
    zzy.drink_soap()
}
```

##### 面向接口编程

你如果了解面向对象，你也一定可以明白什么是面向接口，如果面向对象的方法参数传递的是对象，那么面向接口，就是传递的是接口：

我们来看一个复杂一点的例子：

```kotlin
class Zzy(val gender: String) : Person(22,"zhuzeyu"),Eat
{
// 以上代码省略，新添加一个方法
    fun  eat_drink_together( eat : Eat)
    {
        eat.drink_soap()
        eat_rice()
    }
}
```

在这里调用新方法，传入自己作为参数：

```kotlin
fun main()
{
    var zzy = Zzy(120,100)
    zzy.eat_drink_together(zzy)
}
```

这段代码可能有点稍微难理解他的目的是什么，注意看到`eat_drink_together`方法的参数实际上是一个接口，因为zzy类实现了这个接口当然也可以传入作为参数，顺理成章地调用其中的实现的抽象的方法，这里就可以叫接口的多态。

#### Kotlin的可见性修饰符

kotlin的修饰符存在很大的与java的不同：

可以参考一下表：

![3](https://s2.loli.net/2024/10/04/OYCuzypwlLreh1D.png)

可以看到最大的区别有几点

- Kotlin中默认的类型是public而不是default。
- Kotlin中没有default类型，也就是没有包级可见，Kotlin的protect类型同一包路径下也不可见（牢记没有包可见）。
- 新增internal类型，在同一模块下可见。

可以看到kotlin和android设计首选的语言并不是全无道理，抛弃了原来java的文件构建模式，转而支持更多的android的文件架构，关于模块等到写到的时候再说。

#### Kotlin的数据类和单例类

##### 数据类

数据类常常用于将服务器端或数据库中的数据集映射到内存中，为编程提供数据模型的支持。简单来说就是专门用来管理数据在内存中的类。

在java中为了实现一个数据类我们通常要实现equals（）、hashCode（）方法等。kotlin保持着能省就省的原则，这些没有实际逻辑意义的代码都应该被替换掉，被替换成什么了呢？

就一个关键字：`data`

```KOTLIN
data class Exam(val exameername:String,val examname:String,val examgrade:Int)
```

你可能会疑问为什么后边没有{}呢，在提示一下语法糖，没有代码的话可以省略的。

那么数据类怎么使用呢？

我这里定义的是考试类，参数分别是考试人名称，考试名称，考试成绩。

这里我们创建两个实体对象然后调用，.equals()方法进行匹配：

```kotlin
val exam1 = Exam("zzy","exam1",100)
val exam2 = Exam("zzy","exam1",100)
if (exam1 == exam2)
println("same")
if(exam1.equals(exam2))
println("yesyes")
```

```shell
same
yesyes
```

#####  单例类

单例类是针对单例模式而特殊设计的类，它可以避免创造重复的对象，当我们希望某个类在全局最多只有一个实例的时候们就可以使用单例类。

对于java我们不再赘述，将构造函数私有化等就可以实现单例类，虽然java实现的并不复杂但是kotlin明显做的更好，我们只需要将关键字class改为object即可，不需要自己去写实现方法，操作简单。

演示：

其实在前面我们都是通过直接定义main函数来直接运行的，但是在面向对象的时候大多是是没有函数的概念，这里我们借助单例类来实现类似于java类中的main方法：

```kotlin
object test
{
    @JvmStatic
    fun main(args: Array<String>)
    {
        //继承等演示
        var zzy = Zzy(120,100)
        zzy.eat_drink_together(zzy)
        //数据类演示
        val exam1 = Exam("zzy","exam1",100)
        val exam2 = Exam("zzy","exam1",100)
        if (exam1 == exam2)
            println("same")
        if(exam1.equals(exam2))
            println("yesyes")
        //单例类演示
        test.tester();
    }
    // 单例类方法
    fun tester()
    {
        println("test")
    }
}
```

可以看到创建了一个单例类，并且声明了两个方法，分别是main和tester方法

你可以看到main上边有一个注释@JvmStatic，你可以意识到这里的main其实并不是static的

实际上kotlin并没有static这个关键字，这个先留到以后再说，这里的main就是程序的入口，调用单例类的方法很简单而且不需要自己实例化，在启动的时候kotlin已经自动帮忙创建了所有单例类的实例化，在作用范围内调用很简单：

```kotlin
//单例类演示
test.tester();
```

直接类名+方法即可，注意这里要和java的静态方法区别开，只是结构类似，本质完全不同，并不是静态的数据类型。

































































































