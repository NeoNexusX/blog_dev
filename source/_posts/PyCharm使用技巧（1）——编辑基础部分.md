---
title: PyCharm使用技巧（1）——编辑基础部分
categories:
  - Pycharm
tags:
  - Pycharm
excerpt: "Pycharm编辑基础部分"
date: 2023/10/21 00:00:00
---
# PyCharm使用技巧（1）——编辑基础部分

## 提示：图床在国外且动图比较多的情况下，需要时间加载。

## 目录：

<!-- toc -->

[TOC]

<img src="https://s2.loli.net/2023/10/23/UvSq4AxVNdbzeWC.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1698049838504" style="zoom:67%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1695021721029" style="zoom:67%;" />



## 编辑基础

### Context action (上下文联想动作)  KeyMap：ALT + ENTER

你可以在几乎任何上下文中调用**Context action**。比如在下面代码中：

```python
def method_with_unused_parameter(used, redundant):
    print("It is used parameter: " + str(used))


def intention_example(a, b):
    if not (a and b):
        return 1
    return 2


method_with_unused_parameter("first", "second")
method_with_unused_parameter("used", "unused")
intention_example(True, False)
```

针对上边的代码按下`ALT` + `ENTER`此时PyCharm会根据上下文环境来推测你想要的行动，<img src="https://s2.loli.net/2023/10/22/Wm1EDKLAzbFH4i2.png" alt="image-20231022221515732" style="zoom:150%;" />

这里就推测出第二个参数没有什么用会自动帮你移除第二个参数.

可以看到整个代码部分对应的无用参数都被移除了：

![image-20231022221913479](https://s2.loli.net/2023/10/22/tEnWoFUckKyagmA.png)

同时Context action也能提供逻辑优化等操作：

还是上边的代码，我们注重这一行：

![image-20231022222939162](https://s2.loli.net/2023/10/22/cQ3byIvaHGo8fkB.png)

我们在and这里使用快捷键`ALT` + `ENTER`：根据德摩根法则其可以优化成：

![image-20231022222733594](https://s2.loli.net/2023/10/22/93obW4BqQEcOtRe.png)

### Expand and shrink the code selection（扩展和缩小代码选择） KeyMap：CTRL +  W

有如下代码：

```python
def some_method(first, second, third):
    print(first, second, third)


def example_method(condition):
    if condition:
        print("Begin of the work")
        some_method("first string", "This is a long string that you can select for refactoring", "third string")
        print("End of the work")
    print("The end")

```

你想选择`that`单词，常规做法是双击，在这里你可以使用`CTRL + W`来实现操作：

![image-20231022223705647](https://s2.loli.net/2023/10/22/68gRUyi2tFfehSV.png)

效果如下：

![image-20231022223859938](https://s2.loli.net/2023/10/23/K5Np9UElIOzZwYP.png)

此时你需要选择整个字符串，无需动鼠标，`CTRL+W`即可实现对应操作：

![image-20231022224043310](https://s2.loli.net/2023/10/22/oVF3ZNhwjx7QDMn.png)

当然你连续按下三次`CTRL+W`，你就可选择整个引用内容，包含引号：

![image-20231022231031499](https://s2.loli.net/2023/10/22/CEWYKtswjuA6Pcb.png)

当然你连续按下四次`CTRL+W`，你就可选择整个调用内容：

![image-20231022231548555](https://s2.loli.net/2023/10/22/KyXmx9pB3IZhACa.png)

当然有时候会有不小心按错，使用快捷键`CTRL+Shift+W`就可以回退：

![image-20231022232010023](https://s2.loli.net/2023/10/22/qO2Cj7UDyYV1cNv.png)

在if后边，两次CTRL+W就可以选择整个if表达，十分快捷：

![image-20231022232157648](https://s2.loli.net/2023/10/22/n81dtaj2uXmOQlW.png)

### Duplicate and delete lines（快速复制和删除行）KeyMap：CTRL + D

当你想快速复制某一行代码的时候，如下所示：

![image-20231022233258128](https://s2.loli.net/2023/10/22/hxuCcEXa7es9k5Z.png)

将光标放在此处按下 CTRL+D，快速复制此行：

![image-20231022233542868](https://s2.loli.net/2023/10/22/1lzJn3ySCUhR9NL.png)

当你需要复制多行的时候：

![image-20231022233522113](https://s2.loli.net/2023/10/22/fUzrZhDaXqdQVYW.png)

完成复制：![image-20231022233701314](https://s2.loli.net/2023/10/22/i2AxcjtTroqQm61.png)

快速删除行的时候使用`CTRL+Y`快捷键实现行删除。

### Move code fragments（代码片段快速移动） KeyMap：ALT / CTRL  +  Shift  +  ⬇ / ⬆

重新排序代码往往需要两个过程，剪切和粘贴，而通过Pycharm可以快速实现代码的重排序：

如下代码片段，我们想将输出放到代码更新后：

![2](https://s2.loli.net/2023/10/23/mxWQFl7pPgheBYA.gif)

到这里你也许会感觉有点鸡肋，但是如果他能整个方法，整个结构一起移动呢？

我们将光标放到方法名称前，然后按下快捷键`CTRL + Shift +  ⬆`，实现整个方法的迁移：

![1](https://s2.loli.net/2023/10/23/LiNvx31OsGTWReQ.gif)

### Surround and unwrap （环绕模板与代码结构性删除）KeyMap：ALT + T /  CTRL + Shift + Delete

通常对结构化的代码如`if`等，需要在外围添加`while`或者`trycatch`通常需要进行多步操作，通过环绕代码模板可以快速进行填充，同时也可以使用结构性抹除进行删减，如下面代码：

![3](https://s2.loli.net/2023/10/23/GBq8xD9aFpMhiOW.gif)

### Multiple selection （多重快速选择）KeyMap ：ALT + J  /  CTRL  +  ALT  +  Shift  +  J

通常我们在使用Pycharm的时候也会有需求去处理HTML等非常标签化的语言，修改的时候需要前后标签一起修改，鼠标移动的话非正常不方便，Pycharm提供了较为方便的操作多重选择，多重选择后直接进入了编辑模式可以对多个对象同时编辑，见如下代码：

![4](https://s2.loli.net/2023/10/23/MOfGU7n2ovBZayc.gif)

