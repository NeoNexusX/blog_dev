---
title:  Pytorch--基础指北_伍
categories:
  - Pytorch
tags:
  - Pytorch
excerpt: "介绍了使用PyTorch进行深度学习的基础知识和操作，包括Python编程技巧、DataFrame数据处理、数据加载与批处理、数据集类的构建及其通过Dataloader类的并行加载和操作"
---

# Pytorch ——基础指北_伍

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom:67%;" />

<!-- toc -->

[TOC]



## 软件环境：

- **pytorch 1.10**
- **pycharm**

## 基础知识：

### python 的异常触发

我们可以使用raise语句自己触发异常

raise语法格式如下：

```
raise [Exception [, args [, traceback]]]
```

语句中 Exception 是异常的类型（例如，NameError）参数标准异常中任一种，args 是自已提供的异常参数。最后一个参数是可选的（在实践中很少使用），如果存在，是跟踪异常对象。如果你不懂异常没关系，只要知道是主动的报告代码运行错误就可以。

### python 的列表解析

根据已有列表，高效创建新列表的方式。

列表解析（list comprehension）提供了一种优雅的生成列表的方法，能用一行代码代替十几行代码，而且不损失任何可读性。**而且，性能还快很多很多**（50%）。

它的基本结构是：

```python
[expr for iter_var in iterable if cond_expr]#表达式+for循环+条件表达式
```

构建完成后返回一个list列表，对于第一个表达式甚至可以加入 if else。

举个例子：

```python
numbers = [1,2,3,4,5,6,11,12]
>>>max=10
>>>[ i for i in numbers if i >max ]
>>>[11, 12]
```

通过表达式返回大于10的所有数据。

```python
PyDev console: starting.
Python 3.8.12 (default, Oct 12 2021, 03:01:40) [MSC v.1916 64 bit (AMD64)] on win32
>>>numbers = [1,2,3,4,5,6,7,89]
#返回大于5的构成的列表。
>>>[i if i >5 else 0  for i in numbers]
>>>[0, 0, 0, 0, 0, 6, 7, 89]
>>>[i if i >5 else None  for i in numbers]
>>>[None, None, None, None, None, 6, 7, 89]
#小于5增加100返回
[i if i >5 else i+100  for i in numbers]
[101, 102, 103, 104, 105, 6, 7, 89]
```

### Python的DataFrame

DataFrame是一种表格型数据结构。通过行和列的索引可以快速定位数据位置。

![img](https://s2.loli.net/2024/09/29/D4921OnZizHMUTw.png)

```python
pandas.DataFrame( data, index, columns, dtype, copy)
```

- **data**：一组数据(ndarray、series, map, lists, dict 等类型)。
- **index**：索引值，或者可以称为行标签。
- **columns**：列标签，默认为 RangeIndex (0, 1, 2, …, n) 。
- **dtype**：数据类型。
- **copy**：拷贝数据，默认为 False。

样例：

```python
data = [['zzy', 3], ['jszszzy', 6], ['zzys', 4]]
df = pd.DataFrame(data, columns=['name', 'length'])
print(df)
```

结果：

```shell
      name  length
0      zzy       3
1  jszszzy       6
2     zzys       4
```

如果想访问行可以这样：

```python
print(df.loc[0])
```

访问某两行：

```python
print(df.loc[[0, 1]])
>>>     
	   name  length
0      zzy       3
1  jszszzy       6
#此时相当于一个小DataFrame
```

访问某一列：

```python
print(df["name"])
>>>
0        zzy
1    jszszzy
2       zzys
```

同时限定行和列：

```python
print(df.iloc[0:1,0])
>>>
0    zzy
Name: name, dtype: object
```

可以看到同时支持列号和行号。

```python
print(df.shape)
>>>
(3, 2)
```

[shape](https://so.csdn.net/so/search?q=shape&spm=1001.2101.3001.7020)函数，返回的是元组（不可变的列表）

- hg.shape返回的是hg的行数和列数
- hg.shape[0]返回的是hg的行数，有几行
- hg.shape[1]返回的是hg的列数，有几列

### Python的enumerate

enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中。

```python
enumerate(sequence, [start=0])
```

- sequence -- 一个序列、迭代器或其他支持迭代对象。
- start -- 下标起始位置的值。

我们需要输出下标的同时输出内容时可以使用它：

```shell
>>> seq = ['one', 'two', 'three']
>>> for i, element in enumerate(seq):
...     print i, element
...
0 one
1 two
2 three
```



## Pytorch的数据加载

为什么使用数据加载？在前面几节课我们知道函数关系的情况下可以随机生成数据集，每次训练的时候直接把所有数据都送进去进行加载是不现实的，当数据量巨大的时候你的显存可能撑不住，所以我们就需要一个工具类来实现对数据集的管理，`torch.utils.data.dataset，dataloader`就是这样的类，通过继承这个类并将里面的内容重写，我们能非常方便的实现对数据集的操作。

首先我们需要下载数据集：

[数据集下载](http://archive.ics.uci.edu/ml/datasets/SMS+Spam+Collection)

下载解压后放到目录中：

![1](https://s2.loli.net/2024/09/29/TpkcJNUbgjiBznm.png)

#### 如何导入数据并转换为Dataset（数据集）？

首先我们需要将这个类引用过来：

```python
from caffe2.python.dataset import Dataset
```

 引用以后还需要将其内部的三个方法实现以便于使用：

```python
class Mydataset(Dataset):
    def __init__(self):
    #初始化构造数据集
    def __getitem__(self,index):
    #获取元素    
    def __len__(slef):
    #获取数据总长度   
```

下面展示一下如何实现一个**Dataset**

首先是初始化数据集，实现内部三个方法：

首先是初始化：

```python
    def __init__(self):
        lines = open(data_path, "rb")
        lines = [[i[:4].strip(), i[4:].strip()] for i in lines]#分割数据去除内部空格等冗余信息
        self.df = pd.DataFrame(lines, columns=["label", "sms"])#调整行标
```

​	初始化的思路很简单就是将每组数据结合起来然后转化成DataFrame的形式。

再就是__ getitem__方法：

```python
    def __getitem__(self, index):
        item = self.df.iloc[index,:]
        return item.values[0],item.values[1]
    #这里是获取DataFrame的内容·，返回为字典，调用values分解
```

最后是__ len__方法：

```python
    def __len__(self):
        return self.df.shape[0]
    #这里使用了DataFrame的shape方法获取行数。
```



## 数据集的迭代与操作

通过Dataset我们成功地将一个文本数据导入并对其进行了处理成功转化成一个通用的数据集类。但是我们还有很多操作没有实现：

- 批处理数据（Batching the data）
- 打乱数据（Shuffling the data）
- 使用多线程 `multiprocessing` 并行加载数据。
- 对多余的数据进行处理`drop_last`

在pytorch中如何对数据集进行操作呢？

这就来到了Dataloader类：

### 如何使用Dataloader呢？

使用很简单：

```python
 mydataloader = DataLoader(dataset=mydataset, batch_size=10, shuffle=True, num_workers=2)
```

调用其类的初始化来实例化对象，产生一个mydataloader，在实例化的过程中通过参数来调整数据集。

dataset就是需要操作的dataset对象，

drop_last就是没批的数据大小，

shuffle是否进行随机打乱处理，

num_workers是多线程加载数据集的数量，

drop_last是是否进行丢去数据集的零头，因为数据数量对batch_size来说总不是一定能整除的最后一组可能并不能占满。

```python
mydataloader = DataLoader(dataset=mydataset, batch_size=10, shuffle=True, num_workers=3, drop_last=True)
```

那么我们该怎么查看修改后的数据集呢？

这里需要说明的是，在构造数据的时候我们采用的是两种为一组的方式，也就是：

**标签+数据内容**，在迭代输出的时候也要将两个数据都取出来，你可以直接迭代：

```python
    for label in mydataloader:
        print(label)
```

输出：

```shell
[('ham', 'ham', 'ham', 'ham', 'ham', 'ham', 'spam', 'ham', 'spam', 'ham'), ('Only saturday and sunday holiday so its very difficult:)', "Oh right, ok. I'll make sure that i do loads of work during the day!  got a really nasty cough today and is dry n shot so that should really help it!", "That's one of the issues but california is okay. No snow so its manageable", "How have your little darlings been so far this week? Need a coffee run tomo?Can't believe it's that time of week already …", "Don't fret. I'll buy the ovulation test strips and send them to you. You wont get them til like march. Can you send me your postal address.u'll be alright.Okay.", 'Ok anyway no need to change with what you said', 'PRIVATE! Your 2004 Account Statement for 07742676969 shows 786 unredeemed Bonus Points. To claim call 08719180248 Identifier Code: 45239 Expires', "That's the trouble with classes that go well - you're due a dodgey one … Expecting mine tomo! See you for recovery, same time, same place", 'FREE for 1st week! No1 Nokia tone 4 ur mobile every week just txt NOKIA to 8077 Get txting and tell ur mates. www.getzed.co.uk POBox 36504 W45WQ 16+ norm150p/tone', 'After completed degree. There is no use in joining finance.')]
****************************************************************************************************
[('ham', 'ham', 'ham', 'ham', 'spam', 'spam', 'ham', 'ham', 'ham', 'ham'), ('I only work from mon to thurs but Sat i cant leh... Booked liao... Which other day u free?', 'Do I? I thought I put it back in the box', 'Dont search love, let love find U. Thats why its called falling in love, bcoz U dont force yourself, U just fall and U know there is smeone to hold U... BSLVYL', 'Long after I quit. I get on only like 5 minutes a day as it is.', 'URGENT! We are trying to contact U. Todays draw shows that you have won a £2000 prize GUARANTEED. Call 09058094507 from land line. Claim 3030. Valid 12hrs only', 'Camera - You are awarded a SiPix Digital Camera! call 09061221066 fromm landline. Delivery within 28 days.', 'Ok...', 'Tmrw. Im finishing 9 doors', 'Love it! The girls at the office may wonder why you are smiling but sore...', '"YEH I AM DEF UP4 SOMETHING SAT,JUST GOT PAYED2DAY & I HAVBEEN GIVEN A£50 PAY RISE 4MY WORK & HAVEBEEN MADE PRESCHOOLCO-ORDINATOR 2I AM FEELINGOOD LUV"')]
****************************************************************************************************
```

可以看到每一组数据其实分开了两部分并组合成一个list，list里面是根据batch_size 大小构成的两个元组。

当然你也可以这样迭代，就直接会拆分成两个元组，用起来很方便。

```python
    for (label, sms) in mydataloader:
        print(label, sms)
        print("*" * 100)#分隔符
```

###### 到这里所有的内容就都完成了。
