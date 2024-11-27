---
title:  Pytorch--基础指北_肆
categories:
  - Pytorch
tags:
  - Pytorch
excerpt: "Python面向对象编程通过类定义、`self`引用、实例化对象、访问属性和方法、继承与方法重写，以及内置方法如`__init__`和`__call__`等实现对象的创建与行为。"
date: 2024/09/28 00:00:00
---

# Pytorch ——基础指北_肆

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom:67%;" />

<!-- toc -->

[TOC]



## 软件环境：

- **pytorch 1.10**
- **pycharm**

## 配套代码下载地址：

[gitee-pytorch](https://gitee.com/jszszzy/pytorch-study)

## 基础知识：

### 一点Python

#### Python的面向对象

```python
class ClassName:
   '类的帮助信息'#类文档字符串
   class_suite #类体
```

类的帮助信息可以通过ClassName.__ doc __查看。

**class_suite** 由类成员，方法，数据属性组成。

#### Python的self关键字

**self**代表类的实例，而非类。常用于在类的定义中指代对象。

#### Python创建实例对象

实例化类其他编程语言中一般用关键字 **new**，但是在 **Python** 中并没有这个关键字，类的实例化类似函数调用方式。以下使用类的名称 **Employee** 来实例化，并通过 __init__ 方法接收参数。

```python
对象变量 = 类名()
```

#### Python类的方法

在类的内部，使用 **def** 关键字可以为类定义一个方法，与一般函数定义不同，类方法必须包含参数 self,且为第一个参数。

#### Python访问类的属性和方法

可以使用点号 **.** 来访问对象的属性。使用如下类的名称访问类变量:

```python
emp1.displayEmployee()
emp2.displayEmployee()
print "Total Employee %d" % Employee.empCount
```

#### Python内置类和属性

- __dict__ : 类的属性（包含一个字典，由类的数据属性组成）
- __doc__ :类的文档字符串
- __name__: 类名
- __module__: 类定义所在的模块（类的全名是'__main__.**className**'，如果类位于一个导入模块**mymod**中，那么**className**.__module__ 等于 **mymod**）
- __bases__ : 类的所有父类构成元素（包含了一个由所有父类组成的元组）

#### Python基础方法

| 序号 | 方法名                          | 类型 | 作用                                         |
| ---- | ------------------------------- | ---- | -------------------------------------------- |
| 01   | `__new__(self)`                 | 方法 | **创建对象**时，会被 **自动** 调用           |
| 02   | `__ init__ ( self [,args...] )` | 方法 | **对象被初始化**时，会被 **自动** 调用       |
| 03   | `__del__(self)`                 | 方法 | **对象被从内存中销毁**前，会被 **自动** 调用 |
| 04   | `__str__(self)`                 | 方法 | 返回**对象的描述信息**，`print` 函数输出使用 |

#### Python类的实例化与self参数

##### self参数

- 在 **类的外部**，通过 `变量名.` 访问对象的 **属性和方**
- **调用方法时**，程序员不需要传递 `self` 参数
- 在方法内部，`self` 就表示 **当前调用方法的对象自己**，可以通过 `self.` **访问对象的属性和方法**

##### 实例化的初始化方法

- 当使用 `类名()` 创建对象时，会 自动执行以下操作：
  1. 为对象在内存中 **分配空间** —— 创建对象
  2. 为对象的属性 **设置初始值** —— 初始化方法(`init`)
- 这个 **初始化方法** 就是 `__init__` 方法，`__init__` 是对象的**内置方法**

##### 初始化的同时设置初始值

- 在开发中，如果希望在 创建对象的同时，就设置对象的属性，可以对 初始化方法进行 

  改造：

  1. 把希望设置的属性值，定义成 `__init__` 方法的参数
  2. 在方法内部使用 `self.属性 = 形参` 接收外部传递的参数
  3. 在创建对象时，使用 `类名(属性1, 属性2...)` 调用

#### Pyhton的继承

继承的语法：

```python
class 派生类名（基类名）
```

子类不重写 **__ init__**，实例化子类时，会自动调用父类定义的 **__ init__**。

重写了**__ init__** 时，实例化子类，就不会调用父类已经定义的 **__ init__**，如果重写了要继承父类的方法时可以使用**super**关键字来继承：

```python
super(子类，self).__init__(参数1，参数2，....)
```

super并不是一个函数，是一个类名，形如super(B, self)事实上调用了super类的初始化函数，产生了一个super对象；

super类的初始化函数并没有做什么特殊的操作，只是简单记录了类类型和具体实例；

也可以这样：

```python
父类名称.__init__(self,参数1，参数2，...)
```

举个例子：

```python
class Father(object):
    def __init__(self, name):
        self.name=name
        print ( "name: %s" %( self.name))
    def getName(self):
        return 'Father ' + self.name
 
class Son(Father):
    def __init__(self, name):
        super(Son, self).__init__(name)
        print ("hi")
        self.name =  name
    def getName(self):
        return 'Son '+self.name
 
if __name__=='__main__':
    son=Son('runoob')
    print ( son.getName() )
```

输出结果：

```python
name: runoob
hi
Son runoob
```

#### Pthony的__ call__方法

 [Python](http://c.biancheng.net/python/) 类中一个非常特殊的实例方法，即 __call__()。该方法的功能类似于在类中重载 () 运算符，使得类实例对象可以像调用普通函数那样，以“对象名()”的形式使用。

#### 单下划线、双下划线、头尾双下划线的说明

- **__ foo __**: 定义的是特殊方法，一般是系统定义名字 ，类似 **__ init__()** 之类的。
- **_ foo**: 以单下划线开头的表示的是 protected 类型的变量，即保护类型只能允许其本身与子类进行访问，不能用于 **from module import \***
- **__foo**: 双下划线的表示的是私有类型(private)的变量, 只能是允许这个类本身进行访问了。



## Pytorch API 实现基础模型

### nn.Module

`nn.Module` 是`troch.nn`提供的一个类，是pytorch中我们自己定义网络的一个基类。通过整个基类来实现网络可以调用预先实现好的算法,不需要我们考虑太多就可以快速构建和训练。

##### nn.Module的使用

1、首先要继承，继承后需要调用super的init方法（重写父类初始化方法），通过调用super的init方法来继承父类中的init的参数

```python
from torch import nn
class Lr(nn.Module):
    def __init__(self):
        super(Lr, self).__init__()  #继承父类init的参数
        self.linear = nn.Linear(1, 1) 
```

2、`farward`方法必须实现，用来定义我们的网络的向前计算的过程。

```python
	def forward(self, x):
        out = self.linear(x)
        return out
```

在讨论这一段代码前我们可以先考虑一个问题：

##### 什么是batch_size，什么是features？

- batc_size是指一次训练所取得样本数，每次经过一个batc_size就更新一次参数。
- features这里指的是二维数组的列，指的是特征的数量。
- 样本数量：指的是二维数组的行，就是样本数量。

如果一次送进去一列就是features=1，那么batch_size=样本数量。

回到我们的代码，`nn.Linear`为torch预定义好的线性模型（本质上是一个类），也被称为**全链接层**，传入的参数为输入的数量，输出的数量(in_features, out_features),你可以注意到输入的参数是不算batch_size的行大小，只考虑features的大小。

你可能会疑问，为什么 **self.linear(x)** 是直接调用的对象然后加一个括号？你如果学过**java**就知道方法属于实例，调用方法应该是这样的`linear.__ call__`,这里举例调用的`__call__`方法实际上就是等于`linear(x)`，没错你如果略过了上文的**Pthony的__ call__方法**回去一看便知。

`nn.Module`定义了`__call__`方法，实现的就是调用`forward`方法，即`Lr`的实例，能够直接被传入参数调用，实际上调用的是`forward`方法并传入参数。

##### torch.optim类

**optimizer**类的设计是为了针对**nn.Module**类来实现参数的更新而设计的，可以简单地理解为自动梯度下降器，自动对**nn.Module**中的参数来实现梯度下降等算法来进行更新。

##### 如何构造和使用optimizer？

要构造一个optimizer需要的参数（先拿常规的SGD来说）有两个分别是：

```python
sgd = opt.SGD(params , lr)#params 是需要更新的参数，lr是学习率
```

这样就完成了一个针对params的梯度下降优化器。那么如何使用这个梯度下降优化器呢？

**sgd**的**step**方法就是起到参数更新的作用，当然在参数更新之前需要先将上一次的梯度置零，其同样也帮我们内置好了**zero_grad**方法，实现一键清零。具体的例子会在下边说明，读到这里你会有一个疑问model的参数怎么获取？

##### **model**的参数：

获取**model**的参数：

本文中所创建的**model**类型是基于其现有的线性模型所产生的，要获取模型的参数（这里就是**w**和**b**），直接调用**model**的**parameters**方法即可获取所需的参数。

##### optimizer的使用：

```python
myModel = MyLinear()
#首先创建一个基于SGD的优化器，然后将上面的model的参数传入，完成初始化构造：
optimizer = opt.SGD(myModel.parameters(), learningrate)
# 参数更新前清零梯度
optimizer.zero_grad()
#参数更新
optimizer.step()
```

但你会发现其实缺少了一个步骤，在上一节中我们知道如果神经网络的结尾是一个loss函数就需要从loss开始反向转播来求取梯度，求出梯度以后再进行梯度下降，这里缺少了梯度下降的过程，那么如何计算出loss并进行梯度计算呢？这里就要介绍另一个类：loss。

##### 损失函数类的使用

在torch中预置了许多loss的计算函数比如：

求取均方误差的：

```python
nn.MSELoss()#常用于回归问题
```

求取交叉熵损失的：

```python
nn.CrossEntropyLoss()#常用于分类问题
```

##### 如何使用内置的loss函数的类来进行计算？

对于一个**loss 函数**类来说，需要知道的参数就是**预测值**和**真实值**，对于回归问题我们只需要传入这两个参数就可通过loss函数对象计算出loss函数的结果，其实本质上**loss 函数类**和前边的**nn.Module**一样都是一个类的实例化对象，**nn.Moudule**需要运行的时候（前向传播）调用的是其`__call__`方法， 通过**call**方法来调用**forward**方法实现前向传播，同理那么**loss 函数类**也是不是可以通过相同的办法实现呢？当然可以我们直接调用其对象传入参数就可以完成计算。当计算出**loss**以后如何进行反向传播呢？**loss**函数类返回的也是一个**torch**我们直接调用**backward**方法即可。

```python
lossFun = nn.MSELoss() #构造一个loss类
loss = lossFun(y, y_predicet) #计算loss
loss.backward() #反向传播计算梯度
```



通过这样完成了一次训练，根据训练次数和步长进行迭代训练，代码如下：

```python
import torch
import torch.nn as nn
import torch.optim as opt
import numpy as np
import matplotlib.pyplot as plt

# 0、准备数据
sample_number = 500
learninGrate = 0.001
trainTimes = 20000
x = torch.rand([sample_number, 1])
y = x * 3 + 0.8


# 1、定义模型
class MyLinear(nn.Module):
    def __init__(self):
        # 继承父类的init
        super(MyLinear, self).__init__()
        self.linear = nn.Linear(1, 1)

    def forward(self, x):
        out = self.linear(x)
        return out


# 2、实例模型，优化器类的实例
myModel = MyLinear()
optimizer = opt.SGD(myModel.parameters(), lr=learninGrate)
lossFun = nn.MSELoss()
# 3、训练
for i in range(trainTimes):
    y_predicet = myModel(x)
    optimizer.zero_grad()
    loss = lossFun(y, y_predicet)
    loss.backward()
    optimizer.step()
    if (i % 100 == 0):
        print(loss)
        print("W :", list(myModel.parameters())[0])
        print("B :", list(myModel.parameters())[1])

```

有意思的W和B是generator类的，这样就无法直接获得，需要通过list强制转化，关于generator类会放在下节再将。

输出：

```shell
loss：tensor(5.5467e-08, grad_fn=<MseLossBackward0>)
W : Parameter containing:
tensor([[2.9992]], requires_grad=True)
B : Parameter containing:
tensor([0.8004], requires_grad=True)
```

