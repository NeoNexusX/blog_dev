---
title: Pytorch——基础指北_壹
categories:
  - Pytorch
tags:
  - Pytorch
excerpt: "这是一篇介绍 PyTorch 基础操作、张量使用及 Python 切片的学习笔记。"
---

# Pytorch——基础指北_壹

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom:67%;" />

## **软件环境：**

- **pytorch 1.10**
- **pycharm**
- **CUDA 10.2**

<!-- toc -->

[TOC]



## 一些基础知识：

### python的切片

一个完整的切片表达式包含两个“:”，用于分隔三个参数(start_index、end_index、step)。当只有一个“:”时，默认第三个参数step=1；当一个“:”也没有时，start_index=end_index，表示切取start_index指定的那个元素。

```python
Array[start_index:end_index:step]
```

**step**：正负数均可，其绝对值大小决定了切取数据时的 ‘‘步长”，**而正负号决定了“切取方向”**，正表示 “**从左往右**”取值，负表示“**从右往左**”取值。当step省略时，默认为1，即从左往右以步长1取值。

**start_index**：表示起始索引（包含该索引对应值）；该参数省略时，**表示从对象“端点”开始取值**，至于是从“起点”还是从“终点”开始，则由step参数的正负决定，step为正从“起点”开始，为负从“终点”开始。

**end_index**：表示终止索引（不包含该索引对应值）；该参数省略时，表示一直取到**数据“端点”**，至于是到“起点”还是到“终点”，同样由step参数的正负决定，step为正时直到“终点”，为负时直到“起点”。

举例：

```python
>>> array([0,1,2,3,4,5,6,7,8,9])
>>> array[-1:-6:-1]
>>> [9, 8, 7, 6, 5]
step=-1，从右往左取值，start_index=-1到end_index=-6同样是从右往左取值。
```

### 二维数组的切片

​	多维的切片是在中括号中用逗号运算符, 将不同维上的操作分开，分割开后每个维度上单独操作即可。

```python
>>> array = np.array([[1,2,3,4],[5,6,7,8]])
>>> array[1:3,2:3]
array([[7]])
>>> array[0:3,2:3]
array([[3],
       [7]])
```

## Tensor:
在深度学习里，Tensor实际上就是一个多维数组（multidimensional array）。而Tensor的目的是能够创造更高维度的矩阵、向量。
作为初学者的一些想法：[Tensor的描述](https://zhuanlan.zhihu.com/p/48982978)

### 如何使用

例子 A: 
随机生成一个**张量**
```python
import torch
a = torch.rand(2,2);
print(a)
>>> tensor([[0.8105, 0.5156],
        [0.4006, 0.7924]])
```

例子 B: 

生成一个全是1的张量

```PYTHON
b = torch.ones(2, 2)
print(b)
>>> tensor([[1., 1.],
        [1., 1.]])
```

例子 C:

生成一个全是1的张量，且指定类型是**double**

```python
c = torch.ones(2, 2, dtype=torch.double)
print(c)
>>> tensor([[1., 1.],
        [1., 1.]], dtype=torch.float64)
```

例子 D:

通过Python List 生成一个张量

```python
d = torch.tensor([2.5, 0.1, 1, 1])
print(d)
>>> tensor([2.5000, 0.1000, 1.0000, 1.0000])
```

例子 E:

直接累加

```python
e = torch.rand(2, 2)
print(e)
e.add_(b)
print(e)
>>>
tensor([[0.6713, 0.2451],
        [0.5231, 0.4814]])

tensor([[1.6713, 1.2451],
        [1.5231, 1.4814]])
```

例子 E_2:

直接累乘

```python
e.mul_(a)
print(e)
>>> tensor([[1.3954, 0.9191],
        [0.4332, 0.2947]])
```

例子 F:

 对 张量 进行切片

```PYTHON
f = e[:1, 0].item()
print(f)
>>> 1.3954157829284668
```

例子 G:

重构张量会根据重构大小自我调整 如下 **2 * 2**  变换为  **1*4**

```python
g = e.view(4)
print(g)
>>> tensor([1.3954, 0.9191, 0.4332, 0.2947])
```

例子 H:

将一个张量转换成numpy的一个list ，可以发现其指向的是同一个区域内存，修改其中一个另一个也会跟着改变。

```python
h = g.numpy()
print(type(h))
print(h)
g.add_(1)
print(g)
print(h)
>>>	<class 'numpy.ndarray'>
	[1.3954158  0.91908276 0.4331597  0.29474875]
	tensor([2.3954, 1.9191, 1.4332, 1.2947])
	[2.3954158 1.9190828 1.4331597 1.2947488]
```

例子 特殊:

torch 只支持在同一个设备内存下的运算，只有当默认情况下处于cpu状态的转换到GPU才可进行运算，转换是个很消耗时间的方法。值得一提的是numpy只允许在cpu内存运行。

```python
if torch.cuda.is_available():
    #numpy only support cpu
    device = torch.device("cuda")
    h = torch.ones(5, device=device)
    i = torch.ones(5)
    i = i.to(device)
    i = i.add_(h)
    print(i)
>>> tensor([2., 2., 2., 2., 2.], device='cuda:0')
#转换至CUDA
```

新补充：

torch.max

在分类问题中，通常需要使用`max()`函数对`softmax`函数的输出值进行操作，求出预测值索引，然后与标签进行比对，计算准确率。下面讲解一下`torch.max()`函数的输入及输出值都是什么，便于我们理解该函数。

```
output = torch.max(input, dim)
```

> 输入
>
> - `input`是softmax函数输出的一个`tensor`
> - `dim`是max函数索引的维度`0/1`，`0`是每列的最大值，`1`是每行的最大值

> 输出
>
> - 函数会返回两个`tensor`，第一个`tensor`是每行的最大值；第二个`tensor`是每行最大值的索引。

在多分类任务中我们并不需要知道各类别的预测概率，所以返回值的第一个`tensor`对分类任务没有帮助，而第二个`tensor`包含了预测最大概率的索引，所以在实际使用中我们仅获取第二个`tensor`即可。

例子：

输出每行的最大值和其

```python
>>>
t1 = torch.tensor([[1,2,3,4],[5,6,7,8],[9,10,11,12]])
>>>
print(t1.data.max(1,keepdim = True))
torch.return_types.max(
                        values=tensor([[4],
                                [ 8],
                                [12]]),
                        indices=tensor([[3],
                                [3],
                                [3]]))
```

运行一些操作可能会导致新结果分配到了新内存 

参考文献：

[youtobe教程（需要科学上网）有需要搬运联系我 ](https://www.youtube.com/watch?v=c36lUUr864M)