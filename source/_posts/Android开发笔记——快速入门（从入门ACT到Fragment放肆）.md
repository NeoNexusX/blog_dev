---
title:  Android开发笔记——快速入门（从入门ACT到Fragment放肆2）
categories:
  - Android
tags:
  - Android
  - Fragment
excerpt: "从入门ACT到Fragment放肆2"
date: 2024/10/22 00:00:00
---

# Android开发笔记——快速入门（从入门ACT到Fragment放肆2）

<img src="https://s2.loli.net/2024/10/04/jlUq1gXsIM6Wt8G.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727981351201" style="zoom:50%;" />



<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />



#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]

请先参考前一篇文章复习一下Kotlin的一些语法。

**大部分内容参考了郭霖先生的《第一行代码》，在书的基础上针对目前的实际情况进行实践记录。**

## Fragment的状态和回调

### Fragment的状态

类似于Activity的生命周期，Fragment的生命周期也可能会经历几种状态，只不过在一些细小的地方会有部分区别。

#### 1、运行状态

当一个Fragment所关联的Activity正处于运行状态的时，该Fragment也处于运行状态。

#### 2、暂停状态

当一个Activity进入暂停状态时候（由于另一个未占满屏幕的Activity被添加到了栈底）。与他关联的Fragment就会进入暂停状态。

#### 3、停止状态

在这个状态下Fragment本身是不可见的，这个状态可以由FragmentTransaction的remove() 、replace()方法将Fragment从Activity中移除。但在事务提交前调用了addToBackStack()方法他也会进入停止状态。

#### 4、销毁状态

Fragment总是依附于Activity存在，因此当此Activity被销毁时，与它关联的Fragment就会进入销毁状态，或这个状态可以由FragmentTransaction的remove() 、replace()方法将Fragment从Activity中移除。但在事务提交前**没有**调用addToBackStack()方法他也会进入停止状态

这里放一张官方的图，看起来可能很难理解，不过没事，我们实验一下就会有很深的印象：

![1](https://s3.bmp.ovh/imgs/2022/06/27/6303547cce78e01a.png)

### Fragment的回调

我们可以通过新建一个基础类来方便我们观察Fragment的回调周期。

基础类实现如下：

```kotlin
open class BasicFragment: Fragment() {

     val TAG = javaClass.name.toString();

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(TAG,"This is onCreate")
    }

    override fun onAttach(context: Context) {
        super.onAttach(context)
        Log.d(TAG,"This is onAttach Attach  Activity is ${context.toString()}")
    }


    override fun onResume() {
        super.onResume()
        Log.d(TAG,"This is onResume")
    }

    override fun onPause() {
        super.onPause()
        Log.d(TAG,"This is onPause")
    }

    override fun onStop() {
        super.onStop()
        Log.d(TAG,"This is onStop")
    }

    override fun onDestroyView() {
        super.onDestroyView()
        Log.d(TAG,"This is onDestroyView")
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d(TAG,"This is onDestroy")
    }

    override fun onDetach() {
        super.onDetach()
        Log.d(TAG,"This is onDetach")
    }

}
```

直接选择让他继承Fragment类，再让Fragment继承这个类即可。需要注意的是因为onCreateView方法需要返回对应的View这里需要到对应的Fragment中编写。

本次使用的Fragment是上一篇文章中的LeftFragment，就是单纯的一个Fragment没有什么特殊的通过继承BasicFragment来实现生命周期的输出，代码如下：

```kotlin
class Left_fragment : BasicFragment() {


    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        //onCreateView输出对应的内容
         Log.d(TAG,"This is onCreateView")
         return layoutInflater.inflate(R.layout.left_fragment,container,false)
        
    }

}
```

我们启动看一看有什么结果：

可以看到绑定完成后直接调用到onCreate和onCreateView方法，随着onStart和onResume方法的调用Fragment也被显示出来。

![2](https://s3.bmp.ovh/imgs/2022/06/27/bbae2e7c9a12b8c5.png)

显示结果：

![5](https://s3.bmp.ovh/imgs/2022/06/27/86b2e78bb782594c.png)

当我i们按下返回键退出的时候，会调用：

![3](https://s3.bmp.ovh/imgs/2022/06/27/13746867e9521d7d.png)

这时候在主界面已经不可见。

当我们尝试在后台切换回来的时候,又重新调用了

![4](https://s3.bmp.ovh/imgs/2022/06/27/506ecff72f9c9f3c.png)

下面我们再来考虑一种情况，就**是当一个Fragment被另一个Fragment覆盖的时候**，Fragment的回调都会调用什么？

我们在Activity中创建一个FrameLayout布局作为容器轮流将多个Fragment放入进去，当第二个Fragment显示的时候，会将第一个Fragment、完全覆盖。

如何实现将Fragment替换呢？我们先创建一个方法用来实现替换：

思路很简单，就是通过Activity的`supportFragmentManager`来对Activity中的Fragment进行管理，其中的`transaction`就是可以用来创建一个事务的，通过创建事务实现针对Fragment的操作，具体代码如下：

```kotlin
private  fun replaceFragment( fragment: Fragment)
{
    val fragmentManager = supportFragmentManager
    val transaction = fragmentManager.beginTransaction()
    
    transaction.replace(R.id.Framelayout_another,fragment)
    transaction.addToBackStack(null)
    transaction.commit()
}
```

replace方法传入两个参数一个是容器的ID用来找到容器，另一个是要加载的Fragment。

可以看到在替换之后我们将其装入了返回栈，具体有什么效果接着看。

在Activity中我们为左边按键绑定了一个回调函数，用来触发更换Fragmenmt，具体代码如下：

```kotlin
override fun onCreate(savedInstanceState: Bundle?)
{
    super.onCreate(savedInstanceState)
    binding = ActivityMainBinding.inflate(layoutInflater)
    setContentView(binding.root)
    val left_fragment: View =findViewById(R.id.leftFragment)
    leftbinding =LeftFragmentBinding.bind(left_fragment)
    //绑定替换函数：
    leftbinding.button.setOnClickListener 
    {
        replaceFragment(Another_Fragment())
    }
    replaceFragment( Right_fragment())
}
```

​    先将`Framelayout`填充一个`Right_fragment`最后使用button来替换新的`Another_Fragment`，`Another_Fragment`的布局和类完全和、`Right_fragment`代码相同，只是背景颜色不一样。

打开APP效果如下：

因为我开起了夜间模式，所以所有APP的背景颜色会随着背景进行修改将白色替换成黑色。

可以看到左边是我们的`Left_fragment`右边是我们的`Framelayout`承载的`Right_fragment`。

![7](https://s3.bmp.ovh/imgs/2022/06/27/119146dbcb55e739.png)

这时候`Right_fragment`的回调函数进行如下：和上文的一个Fragment启动过程完全一致。

![6](https://s3.bmp.ovh/imgs/2022/06/27/eb5b1745c78c9ba0.png)

我们点击button触发替换函数，此时他被覆盖结果如下：

![9](https://s3.bmp.ovh/imgs/2022/06/27/ec74e43eee959791.png)

这时候`Right_fragment`的回调函数进行如下：

和上文Fragment和Act一同退至后台不显示的时候不太一样，这里追加调用了一个`DestroyView`用于销毁对应的View对象。因为这个view实际上不会再显示了，注意这里将Fragment添加到了返回栈里面。

![image-20220621182351631](https://s3.bmp.ovh/imgs/2022/06/27/4430bde6cf485ed3.png)

我们用返回键出栈查看结果：

按下返回键，果然返回到了初始界面：

![11](https://s3.bmp.ovh/imgs/2022/06/27/fe59410af2dfb4de.png)

他的回调过程进行如下：

可以看到再调用`onCreateView`方法重新创建了view来进行显示。

![10](https://s3.bmp.ovh/imgs/2022/06/27/1d6f733ba2ed88d8.png)

实际上目前栈顶是最早的`RightFragment`对象我们甚至可以将栈清空看一看有什么效果：

![12](https://s3.bmp.ovh/imgs/2022/06/27/ac4ceb4d36438de8.png)

回调函数执行如下：

也就是说在执行完全退栈的时候会调用`onDestory`和`onDetach`方法。最终实现Fragment的销毁。

![13](https://s3.bmp.ovh/imgs/2022/06/27/945584c8a55166fb.png)

如果我们不使用返回栈来保存呢？

当我们再次返回到`Right_fragment`会怎么样呢？

我们尝试一下：

结果很明显，我们点击替换按钮以后Fragment直接就执行销毁了，我们替换后再去执行返回就会直接退出程序，返回到主界面。

![14](https://s3.bmp.ovh/imgs/2022/06/27/1a910c017f6bee88.png)

最后再放一次目前实验后得到的生命周期图：

![15](https://s3.bmp.ovh/imgs/2022/06/27/232788410f6601cf.png)

总的归结来说，只存在两种情况和一种特殊情况：

- **用户点击返回键**和**fragment没有被添加到返回栈而被替换移除**算是同一种 **当前的Fragment**都会从左边的路径。
- **而添加了返回栈被替换移除** **当前的Fragment** 都会从右边的路径上去。
- 特殊情况就是点击了返回键直接返回到了主界面效果如上图所示。







