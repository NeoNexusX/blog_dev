---
title:  Android开发笔记——快速入门（从入门SQLlite到Room放肆）
categories:
  - Android
tags:
  - Android
  - SQLlite
  - Room
excerpt: "从入门SQLlite到Room放肆"

---

# Android开发笔记——快速入门（从入门SQLlite到Room放肆）



<img src="https://s2.loli.net/2024/10/04/TI4CoLbPNzysXFH.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727981633751" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]

请先参考前一篇文章复习一下Kotlin的一些语法。

**大部分内容参考了郭霖先生的《第一行代码》，在书的基础上针对目前的实际情况进行实践记录。**

## Android的数据持久

Android的数据持久化技术有三种，文件存储、SharedPreferences、数据库存储。

下面先来一 一介绍一下。

## 文件存储

文件存储是最基本的存储方式，他不对存储的内容进行任何格式化处理，所有数据都是原封不动的保存在文件当中的，因此他非常适合**存储一些简单的文本数据或者二进制数据**。

如果想你想通过文件存储的方式来保存一些较为复杂的结构化数据，就需要定义一套属于自己的格式规范。

### 将数据存储到文件当中

Context类当中提供了一个`openFileOutput()`方法，可以用于将指定数据存储到指定的文件中。

这个方法接收两个参数：第一个参数是文件名，在文件创建的时候使用，注意这里的第一个参数是文件名，在文件创建的时候使用，此文件名不包含路径，因此所有的文件都默认存储到/data/data/< package name >/files/目录下；第二个参数是文件的操作模式，主要有 ：

- MODE_PRIVATE:	当输入的文件名相同的时候，所写的内容会覆盖原文件的内容。
- MODE_APPEND:	若该文件已存在，就往里面追加内容，若不存在就创建新文件。

`openFileOutput()`方法返回的是一个`FileOutputStream`对象，通过这个对象就可以使用Java流的方式写入文件中了。

### Java流回顾

流比喻数据的均匀连续性。流这个比喻的本质就是为了让某个对象可以均匀持续地吐出无间隔的数据，或者你可以向这个对象毫无压力地持续不断塞进数据。

我们在这里叙述的流主要是I/O流中的节点流，指的是可以从特定的io设备中读写数据的流。他只能连接数据源进行数据的读写操作。

举个例子：

##### Java的字节流

在计算机当中，无论是文本还是图片或者是音频，都是以二进制，字节的形式存在的，IO流针对这种

形式提供了一系列的流，统称为字节流。

所有的字节流**输入**都继承于Inputstream。所有的字节流**输出**都继承于Outputstream。由于这两个类都是抽象的所以它没办法实例化。

这里就介绍一种来举例：FileInputStream，它可以通过字节流来实现对数据的读入。

结果如下：

![1](https://s3.bmp.ovh/imgs/2022/06/23/c5800227c341e509.png)

需要注意的是路径斜杠与反斜杠都是可以的。

#### Android的文件存储

首先来讲解存储的实现，存储的步骤分为三步，分别是构造打开文件的方式、构造缓冲输出流，关闭文件。

##### 构造文件打开的方式

上文提到过存在两种打开方式：

- MODE_PRIVATE:	当输入的文件名相同的时候，所写的内容会覆盖原文件的内容。
- MODE_APPEND:	若该文件已存在，就往里面追加内容，若不存在就创建新文件。

因为本次是在没有创建文件的前提下，所以优先使用**MODE_APPEND**。

使用API ` val output = openFileOutput("data",Context.MODE_PRIVATE)` 

其中 openFileOutput返回两个参数：

分别是：文件的名字，文件的打开模式。

```kotlin
public FileOutputStream openFileOutput(String name, int mode)
```

最终返回一个FileOutputStream对象，实际上就是一个java的字节流对象。你可能会疑问我的文件创建在哪里呢？实际上这里的默认路径在app路径下单独创建一个File文件夹来存储没有指定路径的内容。

##### 构造缓冲输入流

这个和Java几乎一模一样，都是先构造输出流然后通过输出流构造成包含缓冲区的输出流，代码如下：

```kotlin
val write = BufferedWriter(OutputStreamWriter(output))
```

OutputStreamWriter()输入一个构造好的`FileOutputStream`对象用来将节流输出转化成字符流输出，方便直接写入字符。如果你对字符流还不太清楚，你会想为什么我不能用字节流来存储呢？可看到上文Java从一个UTF-8编码的文件中读取出来的其实是整数，并不是对应的字符。字符流可以参考一下以下文章：

[字节流体系分析](https://www.zhihu.com/question/39262026)。这里就不再赘述。

最后我们使用构造好的write，来写入对应的字符。

我们在这里可以使用kotlin的扩展函数函数来操作，kotlin提供了一个扩展函数方便我们对流进行操作，

```kotlin
write.use {
    it.write(inputText)
}
```

直接使用构造好的对象来调用use，他会在内部lambda表达式完成的时候自动调用close来关闭流，十分方便。最后完整的代码如下,我们将其封装成一个完整的函数来调用：

```kotlin
fun save (inputText :String)
{
    try {
        val output = openFileOutput("data",Context.MODE_PRIVATE)
        val write = BufferedWriter(OutputStreamWriter(output))
        write.use {
            it.write(inputText)
        }
    }catch (e:IOException)
    {
        e.printStackTrace()
    }
}
```

结合我在Activity那篇文章的生命周期的分析，其实在Act的生命周期中onStop是保存数据的最佳时刻，此时是这个Act不可见后的操作，不影响第二个Act的展现。

所以我们在此法中保存对应的数据。

代码如下：

```kotlin
override fun onStop() {
    super.onStop()
    save(binding.datasave.text.toString())
}
```

实现效果如下：

![29](https://s3.bmp.ovh/imgs/2022/06/23/bf294551e954d75b.png)

我们按下返回键，退出程序到主界面，我们从文件浏览器中观察到存储的数据如下：

浏览器中的路径：

data/data/com.example.dataset/files/data

![28](https://s3.bmp.ovh/imgs/2022/06/22/922107f6d4854483.png)

### 从文件中读取数据

从文件中读取数据几乎和写入数据没有什么太大区别，只是读入的时候我，我们可以使用kotlin系统内置的函数来读取对应的内容，具体代码如下：

```kotlin
fun load():String?
{
    val content = StringBuilder();
    try {
        val input = openFileInput("data")
        val reader= BufferedReader(InputStreamReader(input))
        reader.use {
            reader.forEachLine {
                Log.e(javaClass.simpleName.toString(),reader.toString())
                content.append(it)
            }
        }
    }catch (e : IOException)
    {
        e.printStackTrace()
    }
    return content.toString();
}
```

可以看到使用方法也很简单，我们使用openFileInput、InputStreamReader来分别构造对象然后使用BufferedReader最终实现一个带缓冲区的读入流。

openFileInput是通过context返回的一个读入流（FileInputStream），后边就是字节流转换为字符流等一系列常规操作，没有什么好说，我需要特别说明的是，`forEachLine`这个系统的扩展函数，这个函数非常有意思，大多数博主的使用都多多少少存在一些问题，比如本文上边的这种写法，下面就来说一说为什么这种写法是错误的，我们跳转到`forEachLine`函数的实现，来看一看：

```kotlin
/**
 * Iterates through each line of this reader, calls [action] for each line read
 * and closes the [Reader] when it's completed.
 *
 * @param action function to process file lines.
 */
public fun Reader.forEachLine(action: (String) -> Unit): Unit = useLines { it.forEach(action) }
```

可以看到上边的注释，实际上这个函数的操作就是遍历文档的每一行，并将操作映射到每一行上。

函数的参数很有意思，使用的lambda表达式作为参数，这个lambda表达式名字叫`action`接收一个String类型的参数没有返回值，然后它使用了`useLines`，`forEach`函数来配合这个lambda来处理这个函数，我们再看看`forEach`的实现：

```kotlin
/**
 * Performs the given [action] on each element.
 *
 * The operation is _terminal_.
 */
public inline fun <T> Sequence<T>.forEach(action: (T) -> Unit): Unit {
    for (element in this) action(element)
}
```

他的实现也很简单，就是传入一个λ表达式并将调用他的对象中的内容都遍历一遍。那么是谁来调用它呢？

`useLines { it.forEach(action) }`这个就是调用他的东西，这个it是什么呢？

当Lambda的参数只有一个的时候就可以用it来调用对象，我们来看一下实现：

```kotlin
/**
 * Calls the [block] callback giving it a sequence of all the lines in this file and closes the reader once
 * the processing is complete.
 * @return the value returned by [block].
 */
@RequireKotlin("1.2", versionKind = RequireKotlinVersionKind.COMPILER_VERSION, message = "Requires newer compiler version to be inlined correctly.")

public inline fun <T> Reader.useLines(block: (Sequence<String>) -> T): T =
    buffered().use { block(it.lineSequence()) }
```

实际上就是将上边的`forEach`迭代`传入的lambda表达式`作为一个`迭代lambda表达`式传入到`useLines`的参数中，最后调用`lineSequence`方法，这个方法的实现很长，简答来说就是将文档中的每一行加入到一个Sequence里面然后使用`迭代lambda表达`将每一行处理，这是它的功能的实现，那为什么说大家都用错了?

注意看实际上还有两个函数：

 `buffered(),use()`,use是老朋友了，这里就不再详细说明了，你到这里会意识到个问题，我在这里用了use了，为什么外层还要用呢？

所以对于外层来说，正确的用法是：

```kotlin
fun load():String?
{
    val content = StringBuilder();
    try {
        val input = openFileInput("data")
        val reader= BufferedReader(InputStreamReader(input))
            reader.forEachLine {
                content.append(it)
        }
    }catch (e : IOException)
    {
        e.printStackTrace()
    }
    return content.toString();
}
```

你会感到不安，我怎么知道这个流是否关闭了呢？关于如何监测流的关闭，我们晚点再说，继续说

 `buffered()`函数，这个函数的实现也很简单：

```kotlin
Returns a buffered reader wrapping this Reader, or this Reader itself if it is already buffered.
@kotlin.internal.InlineOnly
public inline fun Reader.buffered(bufferSize: Int = DEFAULT_BUFFER_SIZE): BufferedReader =
    if (this is BufferedReader) this else BufferedReader(this, bufferSize)
```

注释写的很清楚，这个函数就是用来检测是否是缓冲流的，不是的话自动给你加上包装，所以在外层我们根本不需要手动去构造缓冲流，修改后代码如下：

```kotlin
fun load():String?
{
    val content = StringBuilder();
    try {
        val input = openFileInput("data")
        val reader= InputStreamReader(input)
            reader.forEachLine {
                content.append(it)
        }
    }catch (e : IOException)
    {
        e.printStackTrace()
    }
    return content.toString();
}
```

这才是正确的使用方法。

接下来讨论如何检测流是否关闭的问题，方法也很简单，我们构造一个内部类来实现检测，这类继承于

`InputStreamReader`，每次关闭的时候肯定要调用 close()方法，我们的思路就是，每次调用这个方法的时候使用Log输出一下表示流关闭了，这样就能直观的看到流关闭。

```kotlin
inner class AddTestStream( input:FileInputStream):InputStreamReader(input)
{
    override fun close() {
        Log.d(javaClass.simpleName.toString(),"File is closed load succeed")
        super.close()
    }
}
```

最后使用上文的内部类替代一下，load函数结果如下：

```kotlin
fun load():String?
{
    val content = StringBuilder();
    try {
        val input = openFileInput("data")
        val reader= AddTestStream(input)
            reader.forEachLine {
                content.append(it)
        }
    }catch (e : IOException)
    {
        e.printStackTrace()
    }
    return content.toString();
}
```

我们打开软件，可以看到在onCreate执行后加载文件内容，并加载完毕后调用了close关闭了流。

![1-1](https://s3.bmp.ovh/imgs/2022/06/23/a2a2a8f9e513deb0.png)















