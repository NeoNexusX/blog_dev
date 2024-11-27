---
title:  Android开发笔记——快速入门（优雅的开发Activity）
categories:
  -  Android
  -  Activity
tags:
  -  Android
  -  Activity
excerpt: "优雅的开发Activity"
date: 2024/10/22 00:00:00
---

# Android开发笔记——快速入门（优雅的开发Activity）

<img src="https://s2.loli.net/2024/09/29/fDGVwPp6OMtaZ5E.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727616671844" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

## 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

请先参考前一篇文章复习一下Kotlin的一些语法。

[TOC]

<!-- toc -->

**大部分内容参考了郭霖先生的《第一行代码》，在书的基础上针对目前的实际情况进行实践记录。**



## 使用Android手机在AS直接进行开发

参考过的文章链接：

[如何在Android12开启开发者模式](https://baijiahao.baidu.com/s?id=1717480652313244278&wfr=spider&for=pc)

[荣耀 打开USB调试 Android开发](https://blog.csdn.net/qq_42851946/article/details/121332064)

首先安装google的驱动：打开SDK管理器：

![23](https://s2.loli.net/2024/09/29/LXJgQSdiZCvPOIE.png)

找到：Google USB Driver下载：

![1](https://s2.loli.net/2024/09/29/XnRos5Embja3Qpx.png)

然后开启设备管理器：

![24](https://s2.loli.net/2024/09/29/Rq27KFcE9xPmjnN.png)

为设备安装驱动：

![25](https://s2.loli.net/2024/09/29/L4JXsKcUrfyxtj1.png)

在本地找到刚下载的驱动，路径如下，需要参考你的具体路径：

![26](https://s2.loli.net/2024/09/29/92UsVMwN8GjelEf.png)

等待安装完成

![3](https://s2.loli.net/2024/09/29/rNuAqF7DBMtvEle.png)

打开开发者模式：

连续点击**内部版本号**：直到提示打开开发者模式：

<img src="https://s2.loli.net/2024/09/29/47nQZH5fvT9qArx.jpg" alt="16" style="zoom:50%;" />



打开如下选项就可以看到开发者选项：

![17](https://s2.loli.net/2024/09/29/Gpe6YnmbuTHX7Aw.png)

打开开发者选项：

![19](https://s2.loli.net/2024/09/29/pcLrnjf6GV4Sbmu.png)



![18](https://s2.loli.net/2024/09/29/wS89kgBdJ5aVNLW.png)

往下拉找到USB配置选项：

![20](https://s2.loli.net/2024/09/29/24C8vLnRpSPlZac.png)

如果不是RNDIS选择改成RNDIS,不知道为什么即使开启**仅充电模式下开启ADB**调试选项，也不无法在仅充电模式下调试，所以手动开启RNDIS稳妥一点。

**如果选择此模式还是没有反应，就在其它模式和本模式切换一下**。

![21](https://s2.loli.net/2024/09/29/ZJEDhVyHjOTcqtu.png)

可以看到AS中出现了设备：

![22](https://s2.loli.net/2024/09/29/Mors4Tiq6fHkpdP.png)



## Activity的启动模式

Android的Activity的启动模式一共有四种。

**standard、sigleTop、sigleTask、singleInstace**。

### standard

standard模式是Activity的默认启动模式，在不显式指定的情况下，所有Activity都会使用这种模式。

举个例子：

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    //此处注意
    Log.d("FirstActivity","Task id is $taskId")
   val  binding = ActivityFirstBinding.inflate(layoutInflater)
    setContentView(binding.root)
    binding.button1.setOnClickListener()
    {
        val intent = Intent(this,FirstActivity::class.java)
        startActivity(intent)
    }
}
```

我们给一个Act设定一个操作当点击按钮的时候再次创建一个一摸一样的Act出来。

![4](https://s2.loli.net/2024/09/29/4KpASe7gfj5a89t.png)

实际上两个ACT的实例句柄并不相同，也就是说ACT中有两个相同的ACT实例。

具体实现模式如下图所示：

![11](https://s2.loli.net/2024/09/29/XU9teNOwGMDQsnF.png)

### sigleTop

对于标准启动模式你会觉得奇怪，明明我的ACT已经在栈顶了为什么还要启动一个重复的呢？

在sigleTop模式下就能解决你这个疑问，在本模式下，当ACT的启动模式指定为sigleTop，再启动之前就会查询栈顶元素，如果发现返回栈顶已经是该ACT，则认为可以直接使用它，不会在创建新的实例。

我们这里使用一个ACT来测试，在第一个ACT中在来创建一个相同ACT，看是否会创建。

![6](https://s2.loli.net/2024/09/29/AP3oEg7iwv2krSM.png)

果然点击多次并没有新的ACT产生。

模式图如下：

![12](https://s2.loli.net/2024/09/29/PkvMnN92j3CDZrY.png)

### sigleTask

在SigleTop模式下你会有一个疑问，如果有两个ACT相互调用怎么办呢？如果我们使用sigleTop模式，在第二个ACT2中调用第一个ACT（此时栈顶是ACT2）仍会导致创建一个和第一个ACT相同的ACT导致资源的浪费，sigleTask就是能完美解决重复创建栈顶ACT的问题。

每次启动ACT的时候就会检查ACT是否存在该ACT的实例，如果发现已经存在则直接使用这个ACT，并将本ACT之上的所有ACT统统出栈，如果发现没有则创建一个新的ACT实例在栈顶。

我们这里创建两种测试，首先创建两个ACT，一个ACT中调用第二个ACT，在第二个ACT中调用第一个ACT，此时应该会把第一个ACT重新唤醒。

![7](https://s2.loli.net/2024/09/29/1PRjmeLucSxDndt.png)

可以看到ACT1被重新唤醒了。

模式图如下：

![14](https://s2.loli.net/2024/09/29/P9hYJliIbgjoqSX.png)

### singleInstance

不同于以上三种模式，singleInstance模式会启用一个新的返回栈来管理新的ACT，那么这样做有什么意义呢？

假设我们的程序中有一个ACT是允许其他程序调用的，如果想实现其他程序和我们的程序可以共享找个ACT的实例，应该如何实现呢？

前三种方式肯定是不正确的，因为他们都是在自己的返回栈的基础上进行操作，每一个应用程序都有自己的返回栈，同一个ACT在不同的返回栈中入栈的时候都必然创建新的实例。而在本模式下，不管是哪个应用程序来访问这个ACT，都会共用一个返回栈，就解决了ACT实例的问题。

我们这里使用三个ACT来实现操作：

将第一个第三个设置为stander启动模式，再将第二个设置成singleInstance启动模式。

我们让第一个调用第二个ACT，第二个调用第三个ACT.

可以通过输出的信息看到：

![9](https://s2.loli.net/2024/09/29/YnSj1cxkMK9HfGA.png)

我们先启动了第一个ACT他的返回栈ID是106，在启动了第二个ACT他的返回栈ID是107，最后启动了第三个ACT他的返回栈是106和第一个返回栈id相同，因为他们三个都是standard模式。

我们在第三个ACT按下返回键，最后我们看到在提示信息里面有ACT1的restart方法的调用，我们在第三个ACT返回却直接返回的是ACT1，这里很好理解因为ACT1和ACT3在同一个返回栈里面。

singleInstance的返回模式图如下：

![13](https://s2.loli.net/2024/09/29/9MWjTyOgXfYKaqF.png)

## 充分管理和了解你的ACT

在阅读之一部分前请先阅读：[关于JAVA的反射与Class](https://www.zhihu.com/question/24304289/answer/38218810)。

### 如何知晓当前ACT的名字？

创建一个`BaseActivity类`，注意是Kotlin的类，并不是创建一个ACT，让他继承`AppCompatActivity类`，并重写`onCreate方法`：

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    Log.d("BaseActivity",javaClass.simpleName)
}
```

使用Log来输出当前的类名，值得注意的是，这里使用的是类似于java的反射机制，你可以注意到当前的类就是`BaseActivity`为什么还要通过`javaClass.simpleName`来输出类名呢？

实际上并不是这样的，我们先看一个结果：

运行一个FirstActivity以后：

![image-20220504101007423](https://s2.loli.net/2024/09/29/ApDtHvN2JnKYQh3.png)

在Koltin中javaclass表示获取当前实例的Class对象，相当于在Java中调用getClass方法。

具体什么是Class类和对象请参考上文链接，简单的来说就是JVM中用来管理每个类的一个专门的管理类，通过这个类可以获取具体的信息包括对应类的存储位置，类的方法，类名等。

我们在重写父类的onCreate方法同时调用了父类的`super.onCreate(savedInstanceState)`方法，这样就相当于在本类中使用了`Log.d("BaseActivity",javaClass.simpleName)`方法，通过class来获取当前的实例名。

只要我们让`BaseActivity`成为所有ACT的父类就能输出类的信息。

当然你也可以这样写：

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    Log.d("BaseActivity",this.toString())
}
```

这样输出内容在创建相同实例的时候更准确：

![10](https://s2.loli.net/2024/09/29/VUTAtxGlkSifBPw.png)

### 随时退出程序

如果你目前栈内已经有了三个ACT，而现在你想检测退出程序对应ACT阶段回调函数的运行效果怎么样，总不能连续按下三次返回按键吧，那如果有很多的ACT，就连续按下多次？当然不现实了。

解决思路很简单，这里创建一个专门的类针对所有类进行管理。

先看个例子，创建了一个`ActitvityCollector`类：

```kotlin
object ActitvityCollector
{
    private val activities = ArrayList<Activity>()
    fun addActivity(activity: Activity)
    {
        activities.add(activity)
    }
    fun removeActivity(activity: Activity)
    {
        activities.remove(activity)
    }
    fun finishALL()
    {
        for (activity in activities)
        {
            if(!activity.isFinishing)
            {
                activity.finish()
            }
        }
        activities.clear()
    }
}
```

这里ActitvityCollector是一个单例类，因为全局实际上只需要一个ActitvityCollector来管理所有ACT就可以了。

我们创建了一个ArrayList来存放当前所有Activity，又声明了三个方法：

- `addActivity` 用于向集合添加现有的ACT。
- `removeActivity` 用于移除某个ACT
- `finishALL` 用于将所有ACT销毁，销毁的时候需要注意当前ACT是否正在被销毁。

接下来我们使用这个三个方法来管理自己的ACT

首先我们要配合`BaseActivity`来管理，首先是`addActivity`我们不需要自己手动每次调用的时候都添加这到集合中，我们配合BaseActivity的`onCreate`就可以在创建的时候自动调用了，修改后的`onCreate`如下：

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    Log.d("BaseActivity",javaClass.simpleName)
    ActitvityCollector.addActivity(this)
}
```

接下来修改BaseActivity的`onDestroy`方法，让他在退出或者销毁的的时候直接把自己从队列里面移除。

```kotlin
override fun onDestroy() {
    super.onDestroy()
    ActitvityCollector.removeActivity(this)
}
```

实现一键退出也很简单，我们在任何需要退出的地方调用：

```kotlin
ActitvityCollector.finishALL()
```

### 启动ACT的最佳写法

在一个ACT中启动到另一个ACT的方法很简单，首先通过intent来构造**意图**，指明需要启动的ACT：

```kotlin
val intent = Intent(this,SecondActivity::class.java)
```

其中this为上下文对象，`SecondActivity::class.java`也是获取对应类的Class对象，为启动传输class参数。

然后通过`startActivity(intent)`或者`startActivityForResult()`将ACT启动起来，如果有数据可以使用Intent来传输，这里就不再赘述。

但是如果在另一个ACT需要启动这个ACT我们该如何传输参数呢？这时候就得跑去阅读代码了或者去问写这个ACT的人，但实际上我们应该把每一个ACT的启动都规整化把它封装一下，这样和别人一起协作的时候你写完的ACT很容易就能让别人利用起来。

在这里我们利用了companion object来实现因为这样方便在调用的时候不需要自己创建一个对象再去调用它的方法，我们本着高内聚低耦合的想法，就把启动方法声明在伴生对象里面。

```kotlin
companion object {
    fun actionStart(context: Context, data1: String, data2: String) {
        val intent = Intent(context, SecondActivity::class.java).apply {
            putExtra("param1", data1)
            putExtra("param2", data2)
        }
        context.startActivity(intent)
    }
}
```

我们通过这个方法把上下文传递进来然后传入要传递的参数最后构造intent实现数据传递和ACT启动。

## Activity的生命周期

《第一行代码》虽然是新的但其中关于Act的生命周期的部分已经发生了很大的改变，我推荐还是具体实验来真正手动去实践一下，实践才是检验真理的唯一标准。

我们在原来BasicActivity的基础上来进行修改，在每个回调之中输出对应的回调函数：

```kotlin
open class BaseActivity : AppCompatActivity() {

    val tag :String = javaClass.simpleName

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(tag,"onCreate")
        ActitvityCollector.addActivity(this)
    }

    override fun onDestroy() {
        Log.d(tag,"onDestroy")
        super.onDestroy()
        ActitvityCollector.removeActivity(this)
    }

    override fun onStart() {
        super.onStart()
        Log.d(tag, "onStart")
    }
    override fun onResume() {
        super.onResume()
        Log.d(tag, "onResume")
    }
    override fun onPause() {
        super.onPause()
        Log.d(tag, "onPause")
    }
    override fun onStop() {
        super.onStop()
        Log.d(tag, "onStop")
    }

    override fun onRestart() {
        super.onRestart()
        Log.d(tag, "onRestart")
    }

}
```

输出的方式很简答，我们通过Log来输出tag是一个成员获取当前类的实例名称，为了让获取Log的结果更加准确，我们要使用正则表达式来过滤掉不必要的信息。所以插入学习一下正则表达式来看看具体结果。

### 正则表达式

在开始之前我们先来学习一下什么是正则表达式，这个在以后调试的时候非常有用。

正则表达式(regular expression)描述了一种字符串匹配的模式（pattern），可以用来检查一个串是否含有某种子串、将匹配的子串替换或者从某个串中取出符合某个条件的子串等。

在线测试网站：https://regexr.com/。

教程地址：[Regex](https://github.com/ziishaned/learn-regex/blob/master/translations/README-cn.md)

#### 基本匹配

正则表达式其实就是在执行搜索时的格式，它由一些字母和数字组合而成。 例如：一个正则表达式 `the`，它表示一个规则：由字母`t`开始，接着是`h`，再接着是`e`。

正则表达式是大小写敏感的，所以`The`不会匹配`the`。

#### 限定符

- **runoo+b**，可以匹配 **runoob、runooob、runoooooob** 等，**+** 号代表前面的一个字符必须至少出现一次（1次或多次）。
- **colou?r** 可以匹配 **color** 或者 **colour**，**?** 问号代表前面的字符最多只可以出现一次（0次或1次），简单来说？代表的就是前一个字符是可有可无的。这里的例子就是u是可有可无的。
- **runo\*b**，可以匹配 **rund**、**runob、runoob、runoooooob** 等，***** 号代表前面的字符可以不出现，也可以出现一次或者多次（0次、或1次、或多次）。
- **ab{2,}c**，表示字符b至少需要出现两次，本质上是**+**的增强版。**ab{2,6}c** 表示b出现次数区间在【2.6】大于6的也不能算匹配。

以上字符都只能作用于一个字符，这是需要注意的。

#### 针对多个字符的运算符

**（ab)+**,是()和+的结合，这里将ab看为一个整体来做+运算。

#### 逻辑运算符

**a(cat|dog)**，表示匹配到a的情况下，后边是cat或者dog都是可以的。

需要注意的是，这里如果这样写：**acat|dog** 匹配结果就变成了：**acat**或者**dog**

如果输入是acatdog,则匹配结果包含两个匹配项。

![12](https://s2.loli.net/2024/09/29/SmxuADRf6Z8X5HJ.png)

#### 字符集

**[ ]**,代表的是字符集，[abc]表示我们要匹配的是含有字符abc的单词，是abc中任意一个字母哦。

特殊用法：

[a-z]表示含有任意一个小写字母即可。

[a-z0-9]表示含有小写字母和数字即可。

[^ 0-9]表示所有不含有数字的单词，包含换行符。

#### 转码特殊字符

反斜线 `\` 在表达式中用于转码紧跟其后的字符。用于指定 `{ } [ ] / \ + * . $ ^ | ?` 这些特殊字符。如果想要匹配这些特殊字符则要在其前面加上反斜线 `\`。

举个例子：

`(com\.)`匹配效果：

![11](https://s2.loli.net/2024/09/29/eYJruWMF12y7qHO.png)

## 锚点

在正则表达式中，想要匹配指定开头或结尾的字符串就要使用到锚点。`^` 指定开头，`$` 指定结尾。

##### ^号

`^` 用来检查匹配的字符串是否在所匹配字符串的开头。

例如，在 `abc` 中使用表达式 `^a` 会得到结果 `a`。但如果使用 `^b` 将匹配不到任何结果。因为在字符串 `abc` 中并不是以 `b` 开头。

`^(T|t)he` 匹配以 `The` 或 `the` 开头的字符串

匹配结果：

![13](https://s2.loli.net/2024/09/29/wFtMEofDlqU8pmW.png)

**注意这里是这个字符串的开头，不是单词的开头。**



在上边基础类得基础上，我们为每一个周期的回调函数添加一个状态输出代码如下：

#####  `$` 号

同理于 `^` 号，`$` 号用来匹配字符是否是最后一个。

例如，`(at\.)$` 匹配以 `at.` 结尾的字符串。

![14](https://s2.loli.net/2024/09/29/ug62VsdMfGbXZDI.png)

###  简写字符集

| .    | 除换行符外的所有字符                               |
| ---- | -------------------------------------------------- |
| \w   | 匹配所有字母数字，等同于 `[a-zA-Z0-9_]`            |
| \W   | 匹配所有非字母数字，即符号，等同于： `[^\w]`       |
| \d   | 匹配数字： `[0-9]`                                 |
| \D   | 匹配非数字： `[^\d]`                               |
| \s   | 匹配所有空格字符，等同于： `[\t\n\f\r\p{Z}]`       |
| \S   | 匹配所有非空格字符： `[^\s]`                       |
| \f   | 匹配一个换页符                                     |
| \n   | 匹配一个换行符                                     |
| \r   | 匹配一个回车符                                     |
| \t   | 匹配一个制表符                                     |
| \v   | 匹配一个垂直制表符                                 |
| \p   | 匹配 CR/LF（等同于 `\r\n`），用来匹配 DOS 行终止符 |

### 贪婪匹配与惰性匹配 (Greedy vs lazy matching)

正则表达式默认采用贪婪匹配模式，在该模式下意味着会匹配尽可能长的子串。我们可以使用 `?` 将贪婪匹配模式转化为惰性匹配模式。

贪婪匹配，指的是每次匹配都选择匹配最多的内容：

![15](https://s2.loli.net/2024/09/29/hf1XMRBEyPUw8cj.png)

如果需要在第一个地方就筛选出来（尽量短的匹配），就要配合？来实现：

![16](https://s2.loli.net/2024/09/29/Od8MelFQc3bZDon.png)

学会了以后我们看看在AS中如何使用正则表达式来进行调试信息的输出：

选择下拉栏的Logcat模块：

![18](https://s2.loli.net/2024/09/29/RmrhSsLvABxTX8H.png)

选择自定义效果：

![19](https://s2.loli.net/2024/09/29/yGfPcQYErJdsIeV.png)

在Tag中定义自己需要的Tag：

![17](https://s2.loli.net/2024/09/29/Qxns8yGIacq7z5A.png)

这样就可以自定义看到想要看到的结果啦。

这里我让他显示了FirtAct和SecondAct的所有信息。

我们在FirstAct中调用SecondAct然后在一步一步通过返回按键返回到主界面（退出程序）。

开启程序主Act完成：

界面如下：

![22](https://s2.loli.net/2024/09/29/adZutNsRQcbqwf4.png)

此时Log中的输出为：

![20](https://s2.loli.net/2024/09/29/V9fYIqNGaBbv4Ud.png)

点击button打开第二个界面：

![21](https://s2.loli.net/2024/09/29/ZDbP8Oujm3MV6l1.png)

此时输出为：

![24](https://s2.loli.net/2024/09/29/1ceQqrZHlKOuENy.png)

可以看到FirstAct实际上在SecondAct完全就绪的时候才会调用onStop方法。

我们使用返回键返回到第一个Act，结果如下：

![25](https://s2.loli.net/2024/09/29/WXaLnYKo7wvizVE.png)

这时候在SecondAct返回时先调用了onPause方法，直到完全被覆盖才调用onStop和onDestory。

我们再次按下返回键这时候就要返回到主界面了，结果会是什么呢？

![26](https://s2.loli.net/2024/09/29/buTEKCoqjAt5fxH.png)

在返回主界面以后会有会连续调用**onPause**和**onStop**，但是没有调用**onDestory**！

这是和原来完全不一样的，也就是说，退出程序并不会去调用**onDestory。**

还有一个细节就是，onPause是在退出前调用的，onStop是在被完全销毁退出后调用的。

最后放一张完全的图流程图：

![27](https://s2.loli.net/2024/09/29/T4BpjRCSZYP8A7O.png)

总结Act的生命周期只有三点需要详细记忆。

- **前台生命周期：指的是在onResume到onPause之间的过程，在这个过程之间Act是正在运行的，可以交互和操作。**
- **可见生命周期：指的是onStrart到onStop方法之间的过程，在onStop之前都是可见的，在不可见之后会执行onStop()。**
- **OnDestory方法：他的情况在新版之后有些特殊，只有在返回栈退栈的时候才会调用方法，退出程序也不会执行。**









































