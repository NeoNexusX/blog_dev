---
title: Android开发笔记——快速入门 (Kotlin的延迟初始化与Viewbinding、密封类)
categories:
  -  Android
tags:
  - Android
  - Viewbinding
excerpt: "Kotlin的延迟初始化与Viewbinding、密封类"
date: 2024/10/22 00:00:00
---

# Android开发笔记——快速入门 (Kotlin的延迟初始化与Viewbinding、密封类)

<img src="https://s2.loli.net/2024/09/29/kAI59OWGPqluiQN.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727618527687" style="zoom:50%;" />



<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]



## Kotlin的延迟初始

鉴于Kotlin的判空机制，在编译的时候就会检测变量是否存在空的可能，这就导致了一个麻烦，你需要额外多写代码来处理那些可能为空的类，即使在逻辑上他也不可能是NULL的，kotlin的判空机制还是会强制让你增加判空，不过这也不是没有解决办法，下面介绍一个关键字延迟初始化：`lateinit`

当你遇到这样一种场景：

```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var binding :ActivityMainBinding
    
     override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding= ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
    }

}
```

我想在MainActivity类里面调用一个viewbinding，可是这个viewbinding只有在oncreate方法里面才能对他进行初始化你如果在类里面直接进行初始化就会产生错误，实际上绑定类调用的`layoutInflater`并没有在ACT刚开始的时候就完成了初始化，你如果直接调用就会导致报错：

![2](https://s3.bmp.ovh/imgs/2022/06/23/facbd8d00ebeb0e3.png)

```shell
 Caused by: java.lang.NullPointerException: Attempt to invoke virtual method 'android.view.LayoutInflater android.view.Window.getLayoutInflater()' on a null object reference
    at android.app.Activity.getLayoutInflater(Activity.java:5310)
    at com.example.listviewtest.MainActivity.<init>(MainActivity.kt:16)
    at java.lang.Class.newInstance(Native Method)
    at android.app.AppComponentFactory.instantiateActivity(AppComponentFactory.java:95)
    at androidx.core.app.CoreComponentFactory.instantiateActivity(CoreComponentFactory.java:45)
    at android.app.Instrumentation.newActivity(Instrumentation.java:1287)
    at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:4148)
    at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:4463) 
    at android.app.servertransaction.LaunchActivityItem.execute(LaunchActivityItem.java:111) 
    at android.app.servertransaction.TransactionExecutor.executeCallbacks(TransactionExecutor.java:149) 
    at android.app.servertransaction.TransactionExecutor.execute(TransactionExecutor.java:103) 
    at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2665) 
    at android.os.Handler.dispatchMessage(Handler.java:109) 
    at android.os.Looper.loopOnce(Looper.java:206) 
    at android.os.Looper.loop(Looper.java:296) 
    at android.app.ActivityThread.main(ActivityThread.java:8899)
```

如果你对Context不够了解，你会很奇怪到底为什么？

不妨先推荐你按照如下顺序阅读下面几篇文章：

因为获取绑定类的时候使用的是layoutInflate所以关键问题就在于layoutInflate之上：

[再看LayoutInflater](https://blog.csdn.net/guolin_blog/article/details/121889703?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165215148016780357214066%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=165215148016780357214066&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-2-121889703-null-null.nonecase&utm_term=layoutinflater&spm=1018.2226.3001.4450)

[Android LayoutInflater原理分析，带你一步步深入了解View](https://blog.csdn.net/guolin_blog/article/details/12921889?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165215148016780357214066%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=165215148016780357214066&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-12921889-null-null.nonecase&utm_term=layoutinflater&spm=1018.2226.3001.4450)。

等你看完了以后你会意识到LayoutInflater本质上和Context脱不了干系，每次都调用**LayoutInflater的from()**方法去获取一个LayoutInflater的实例，为什么要传context呢？

其实本质上只有一种写法：

```kotlin
LayoutInflater layoutInflater = (LayoutInflater)context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
```

只是Android给我们做了一下封装而已。得到了LayoutInflater的实例之后就可以调用它的inflate()方法来加载布局了。这里不在多讨论这个，我们再来看一下什么是Context：

[Android Context完全解析](https://blog.csdn.net/guolin_blog/article/details/47028975?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165232423116782248586882%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=165232423116782248586882&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-6-47028975-null-null.nonecase&utm_term=onCreate&spm=1018.2226.3001.4450)。

![img](https://s3.bmp.ovh/imgs/2022/06/23/1df15d4972330b74.png)

对于ACT来说，执行过程也是大差不大，但是，ACT的layoutInflate是调用的方法获取的对象，为什么会和本ACT中的Context有关系呢？

我们再来看一下ACT到底如何获取LayoutInflate对象：

[获取LayoutInflater的三种方式分析](https://blog.csdn.net/ruancoder/article/details/51760942?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165232543116782350911197%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165232543116782350911197&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-51760942-null-null.142^v9^pc_search_result_control_group,157^v4^control&utm_term=getLayoutInflater&spm=1018.2226.3001.4187)。

可以看到本质上ACT调用的还是`LayoutInflater.from(context)`方法，而context方法在onCreate方法之前是没有进行加载的，所以会产生NULL报错。

### 那么到底该怎么用？

回到最初的问题，那我们该怎么办呢？在合适的位置进行初始话的话就要使用lateinit关键字，通过lateinit就可以把对应的全局变量在合适的位置进行初始化。

当然Kotlin也提供了一个固定语法来检测在某个位置的全局变量，注意！是全局变量是否进行了初始化代码如下：

```kotlin
companion object
  {
      private lateinit var tester: Zzy
      var signal : Boolean = true

      fun laterinit_tester()
      {
          if(!::tester.isInitialized) {
              tester = Zzy()
              //如果你不太清楚Zzy类是什么或者不太懂Kotlin的类请参考我的专栏文章关于Kotlin的类
          }

          //Log.d("tester", tester.gender)
          println(tester.gender)
      }
}
```

`::tester.isInitialized`来获取对应变量是否进行了初始化，我们在对他取！就可以完成对他的判断了，在没有初始化的地方对其进行初始化。

zzy类的代码：

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
    //方法调用省略this关键字演示如下：
    fun this_test()
    {
        eat_empty()
    }
    fun this_tester2()
    {
        this_test()
    }
}
```

最后运行结果如下：

![4](https://s3.bmp.ovh/imgs/2022/06/23/b053645a3d413608.png)

最后完整的代码在我的Gitee上全部开源：[Gitee地址](https://gitee.com/jszszzy/android_kotlin)。下载压缩包即可。

## Kotlin的密封类和When

先来回忆一下，When是如何使用的，请参考：[When的用法](https://blog.csdn.net/qq_20540901/article/details/123688687?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165234161016781685335776%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=165234161016781685335776&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-2-123688687-null-null.nonecase&utm_term=when&spm=1018.2226.3001.4450)。

我们新建一个类，用来反馈某些操作的结果：

```kotlin
interface Result
class  Success(val msg :String) :Result
class  Failure(val error :Exception) :Result
```

可以看到**Success、Failure**都是继承于Result接口的实现。

我们再来创建一个方法用识别返回的是成功还是失败。

```kotlin
fun When_tester(result: Result)
{
    when(result)
    {
        is Success -> println(result.msg)
        is Failure -> println(result.error)
    }
}
```

写完以后你觉得需要简写一下，因为函数只有一条语句，于是改成如下操作：

```kotlin
fun When_tester(result: Result)
 =   when(result)
    {
        is Success -> println(result.msg)
        is Failure -> println(result.error)
    }
```

万恶的编译检查竟然给你报错！

```kotlin
e: D:\Android\Projects\CodeWithKotlin\app\src\main\java\com\example\codewithkotlin\Utils.kt: (25, 14): 'when' expression must be exhaustive, add necessary 'else' branch
```

问题不大，实际上的简写原则是这样的：

当你的函数体只有 **一块** 作为返回值的代码时，**可以直接省略大括号，用等号来连接,并且省略return关键字，因为等号足以包含返回值的意思**。

也就是说你这样写他也是报错的：

```kotlin
fun When_tester(result: Result)
{
    return  when(result)
    {
        is Success -> println(result.msg)
        is Failure -> println(result.error)
    }
}
```

![5](https://s3.bmp.ovh/imgs/2022/06/23/5ea08f7acc81ce69.png)

实际上你的类就这样两种可能，基本没有可能出现else内的情况，但是！万恶的编译检查竟然给你报错！

不过Kotlin也针对这种情况给出了解决办法：**密封类**。

使用方法很简单我们把对应的接口更改成sealed class即可：

```kotlin
sealed class Result 
class  Success(val msg :String) :Result()
class  Failure(val error :Exception) :Result()
```

记得把括号带上，因为这时候是class需要调用父类的构造方法。

修改好以后就发现报错消失了。可以愉快的避免else的编译检查了。

最后完整的代码在我的Gitee上全部开源：[Gitee地址](https://gitee.com/jszszzy/android_kotlin)。下载压缩包即可。
