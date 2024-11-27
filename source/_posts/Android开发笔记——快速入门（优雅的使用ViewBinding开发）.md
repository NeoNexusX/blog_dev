---
title:  Android开发笔记——快速入门（优雅的使用ViewBinding开发）
categories:
  - Android
tags:
  - Android
  - SQLlite
  - Room
excerpt: "优雅的使用ViewBinding开发 "
date: 2024/10/22 00:00:00
---

# Android开发笔记——快速入门（优雅的使用ViewBinding开发）

<img src="https://s2.loli.net/2024/10/04/BfMY6Trva4pPHWh.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727982152154" style="zoom:50%;" />



<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]

请先参考前一篇文章复习一下Kotlin的一些语法。

**大部分内容参考了郭霖先生的《第一行代码》，在书的基础上针对目前的实际情况进行实践记录。**

## 丢掉findViewById()

在简单的情况下获取Act中的某个控件是很简单的，只需要通过findViewById()就可以获取控件，但是当一个页面有10多个控件的话，事情就变得麻烦了，总不能一直使用findViewById()来调用吧，一点也不优雅。

我们来使用一个新的方案，并不是kotlin-extensions的插件的自动引用，这个插件在新版的AS中已经被淘汰掉了，这里介绍如何使用ViewBinding。

通过视图绑定功能，您可以更轻松地编写可与视图交互的代码。在模块中启用视图绑定之后，系统会为该模块中的每个 XML 布局文件生成一个**绑定类**。绑定类的实例包含对在相应布局中具有 ID 的所有视图的直接引用。

### 与 findViewById 的区别

与使用 `findViewById` 相比，视图绑定具有一些很显著的优点：

- **Null 安全**：由于视图绑定会创建对视图的直接引用，因此不存在因视图 ID 无效而引发 Null 指针异常的风险。此外，如果视图仅出现在布局的某些配置中，则绑定类中包含其引用的字段会使用 `@Nullable` 标记。
- **类型安全**：每个绑定类中的字段均具有与它们在 XML 文件中引用的视图相匹配的类型。这意味着不存在发生类转换异常的风险。

这些差异意味着布局和代码之间的不兼容将会导致构建在编译时（而非运行时）失败。

### 与数据绑定的对比

视图绑定和[数据绑定](https://developer.android.google.cn/topic/libraries/data-binding?hl=zh-cn)均会生成可用于直接引用视图的绑定类。但是，视图绑定旨在处理更简单的用例，与数据绑定相比，具有以下优势：

- **更快的编译速度**：视图绑定不需要处理注释，因此编译时间更短。
- **易于使用**：视图绑定不需要特别标记的 XML 布局文件，因此在应用中采用速度更快。在模块中启用视图绑定后，它会自动应用于该模块的所有布局。

反过来，与数据绑定相比，视图绑定也具有以下限制：

- 视图绑定不支持[布局变量或布局表达式](https://developer.android.google.cn/topic/libraries/data-binding/expressions?hl=zh-cn)，因此不能用于直接在 XML 布局文件中声明动态界面内容。
- 视图绑定不支持[双向数据绑定](https://developer.android.google.cn/topic/libraries/data-binding/two-way?hl=zh-cn)。

考虑到这些因素，在某些情况下，最好在项目中同时使用视图绑定和数据绑定。您可以在需要高级功能的布局中使用数据绑定，而在不需要高级功能的布局中使用视图绑定。

### 如何使用：

视图绑定功能可按模块启用。要在某个模块中启用视图绑定，请将 `viewBinding` 元素添加到其 `build.gradle` 文件中，如下例所示：

![屏幕截图 2022-05-02 105711](https://s2.loli.net/2024/10/04/2783sTxLDlOPhHd.png)

添加完成以后记得将配置信息同步。

为某个模块启用视图绑定功能后，系统会为该模块中包含的每个 XML 布局文件生成一个绑定类。每个绑定类均包含对根视图以及具有 ID 的所有视图的引用。系统会通过以下方式生成绑定类的名称：将 XML 文件的名称转换为驼峰式大小写，并在末尾添加“Binding”一词。

举个例子我有一个**FirstActivity**和他的布局文件**activity_first.xml**，那么对应生成的绑定类就是：**ActivityFirstBinding**

如何使用呢？

先来看一下Firstactivity布局中都有什么？

![image-20220502110141198](https://s2.loli.net/2024/10/04/eVPq7nBbMyKO68Y.png)

很简单就只有一个**button。**

**ActivityFirstBinding**类目前只有个字段：名为 `button` 的 `Button`。我们先创建这个类的实例，最好创建在**FirstActivity**的成员中，类似如下：

```kotlin
class FirstActivity : BaseActivity() {

    private lateinit  var  binding :ActivityFirstBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d("FirstActivity","Task id is $taskId")
        binding =ActivityFirstBinding.inflate(layoutInflater)
       //此处注意
        setContentView(R.layout.activity_first)
    }
}
```

先创建实例：

调用生成的绑定类中包含的静态 `inflate()` 方法。此操作会创建该绑定类的实例以供 Activity 使用。

`ActivityFirstBinding.inflate(layoutInflater)`

这样就完成了调用，接下来就可以愉快的使用binding了。

具体使用也很简单，比如这里我想针对button控件注册一个回调函数怎么办呢？

```kotlin
binding.button1.setOnClickListener{}
```

直接使用`binding.引用控件的ID`，这样就变得优雅了许多。

但是使用的时候还需要注意下 一个问题：

在上一个代码中实际上存在一个问题，就是`setContentView(R.layout.activity_first)`实际上还是用的通过资源文件的引用将其设定为活动的试图，这样确实能将目前活动的view设定为当前的view，但是你要意识到你的button1的回调函数实际上是在`binding.button1.setOnClickListener{}`也就是说它是通过binding的实例来实现的，你如果直接使用`R.layout.activity_first`view视图是不会把回调注册进去的，怎么解决这个问题呢？

通过调用 `getRoot()` 方法或使用 [Kotlin 属性语法](https://kotlinlang.org/docs/reference/properties.html#declaring-properties)获取对根视图的引用。再将根视图传递到 [`setContentView()`](https://developer.android.google.cn/reference/kotlin/android/app/Activity?hl=zh-cn#setcontentview_1)，使其成为屏幕上的活动视图。

完整代码如下：

```kotlin
class FirstActivity : BaseActivity() {

    //不可以在此处直接初始化，会造成空指针问题。
    private lateinit  var  binding :ActivityFirstBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d("FirstActivity","Task id is $taskId")
        binding =ActivityFirstBinding.inflate(layoutInflater)
        //注册回调
        binding.button1.setOnClickListener{
            SecondActivity.actionStart(this, "data1", "data2")
        }
        //设置视图
        setContentView(binding.root)
    }
}
```

还有一个问题就是不可以在类开始直接初始化：

```kotlin
class FirstActivity : BaseActivity() {

    
	//此处初始化报错，空指针问题
   private var  binding=ActivityFirstBinding.inflate(layoutInflater)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        //此处注意
        Log.d("FirstActivity","Task id is $taskId")
        //binding =ActivityFirstBinding.inflate(layoutInflater)
        setContentView(binding.root)
        binding.button1.setOnClickListener{
        }
    }
}
```

运行结果如下图：

![image-20220502113924324](https://s2.loli.net/2024/10/04/vaVP3SMnXIxQLbk.png)





