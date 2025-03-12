---
title: PyCharm使用技巧（2）——代码补全
categories:
  - Pycharm
tags:
  - Pycharm
excerpt: "Pycharm代码补全"
date: 2023/10/21 00:00:00
---

# PyCharm使用技巧（2）——代码补全

## 提示：图床在国外且动图比较多的情况下，需要时间加载。

## 目录：

<!-- toc -->

[TOC]

<img src="https://s2.loli.net/2023/11/23/wy1u6H7pXNJfIdx.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1700749534081" style="zoom:67%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1695021721029" style="zoom:67%;" />



## 代码补全

### Basic completion （基本补全）  KeyMap：ALT + A 

默认情况下PyCharm会自动给你进行提示，当日输入内容的时候就会进行补全，但是某些情况下并不会立马给你提示，尤其是在你没有输入的时候，那么如何主动唤醒补全呢？

在我的设置中可以使用快捷键`ALT + A` 来实现主动唤醒，效果如下：

![4](https://s2.loli.net/2023/11/23/M13iBtAVOxPEb2u.gif)

#### 注意

注意这里和编辑基础的**Context action (上下文联想动作)**  KeyMap：ALT + ENTER 并不一样，Context action 推测的是动作，补全针对的是内容。

下图展示了如果使用Context action 的效果：

![image-20231123210159827](https://s2.loli.net/2023/11/23/7ec5LU648ZRwqQO.png)

### Tab completion （Tab补全）  KeyMap：ALT + A  + Tab  /  Enter

在基本补全产生的浮空标签页中，我们不仅可以针对需要写的内容进行补全，也可以对已经存在的某一个或者某一类对象进行修改，下图展示了如何针对已存在对象进行补全：

下图为了修改`current`为`total`我们使用主动唤醒补全来实现，然后按下`ALT + A  + Tab` 来实现替换，但是并不着急，这里并不是说只能实现替换功能，我们也可以通过`ALT + A  + Enter`来实现插入功能，正如动图前边的内容所展示的插入操作：

![4](https://s2.loli.net/2023/11/23/qsJceV6GCdyubzp.gif)

#### 注意

注意光标所在位置所产生的影响，动图的中也展示了光标进行左右移动的时候浮空标签页所展示内容也是不同的。

### Postfix Completion  （后缀补全）   KeyMap：.

后缀补全，后翎补全有助于在写代码时成少向前转光标您可以根据添加的后绍、表达式的类型及其上下文，将已键入的表达式转换成另一种形式在图括号后面罐入`.`以查看建议列表。

下图展示了如何从表达式后边直接修改表达式内容。

![5](https://s2.loli.net/2023/11/23/ZMPthgbOIFC7sW3.gif)

### Type-matching completion  （类型匹配补全） KeyMap: Ctrl + Shift + Space (空格)  

我最讨厌的PyCharm的就是整个教程讲的一点也不清楚，但是至少他还给你做个教程，属实难绷，下面这里就是让人最理解的地方：

这里引入了Type-matching completion，虽然名字叫这个，但实际上在教程上起了一个更加迷惑的名字叫**Smart Completion**，当使用Basic completion 基本补全无效的时候，也就是说无法直接获取对象的信息的时候，可以使用`Ctrl + Shift + Space`来实现通过上下文信息推断内容，这里不是很好用，仅限在于基本补全无法使用的时候比较好用。

![image-20231123215819686](https://s2.loli.net/2023/11/23/qBFgcLrAEolTs4t.png)

具体操作见下图：

![6](https://s2.loli.net/2023/11/23/BrvsjOzaLp3FVwt.gif)

### F-string completion  F-string补全  KeyMap:None

PyCharm已经实现了支持F-string的补全，F-string是什么？

详见：[7. Input and Output — Python 3.12.0 documentation](https://docs.python.org/3/tutorial/inputoutput.html#tut-f-strings)

理解了F-string这里就不赘述了。

![7](https://s2.loli.net/2023/11/23/ErUegW1pAoX7l3c.gif)
