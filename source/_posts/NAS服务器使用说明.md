---
title: NAS服务器使用说明
---

# NAS服务器使用说明

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

Version:1.0

Date: 2024.04.23

Authors：Tr，NeoNeuxs

<!-- toc -->

## 基本的网络要求

网络要求需要在校园网中，在校园网外需要使用VPN来进行上传。上传文件的速度取决于你的链接速度，推荐使用网线上传。

为什么我的网速很慢？打开校园网链接信息，网线连接的用户，一般不用考虑这个问题，此处可以跳过。

<img src="https://s2.loli.net/2024/04/23/CWBdyLkx72UXKna.png" alt="image-20240423215045985" style="zoom:67%;" />

查看连接信息：

<img src="https://s2.loli.net/2024/04/23/S2b6eO89k7hdFAH.png" alt="image-20240423215142791" style="zoom:80%;" />

应连接到5HZ频段，2.4Ghz频段速度较低，较慢的情况如下：

<img src="https://s2.loli.net/2024/04/23/8G6U7LedFmag9z4.png" alt="image-20240423215305320" style="zoom:67%;" />

**72Mbps相当于10M左右的网速，如果上传100GB的数据那就需要100*1000/10 = 10000s，也就是2.78个小时才能完成上传。**所以使用的时候请注意。

如何从2.4Ghz切换到5Ghz？首先电脑支持5Ghz频段才能使用，2.4Ghz频段切换方法如下：

![image-20240423215639861](https://s2.loli.net/2024/04/23/j2pdsZQyIFPi7C8.png)

点击**断开连接**后再次点击连接就能查看所连接的信息是否改变，没有改变的情况下，多次尝试即可。

## 目录结构

![image-20240424234753210](https://s2.loli.net/2024/04/24/9Yz6UONRdgisnwT.png)

第一级目录存放了两个文件夹，分别是我个人的用户文件夹，**公用的数据文件夹Dataset**，一般要求如下：

![image-20240424235033899](https://s2.loli.net/2024/04/24/T1PUYLxzKVSsNkl.png)

## 如何建立映射来上传内容

1.打开我的电脑，并在左侧右击“此电脑”，然后点击“映射网络驱动器”。

![image-20240423202540683](https://s2.loli.net/2024/04/23/zVMEx1q9O4QJt2m.png)

2.弹出弹窗，点击“连接到可用于存储文档和图片的网站”。

![image-20240423203018882](https://s2.loli.net/2024/04/23/A1ihgnZuxBNJY97.png)

3.点两次下一步。

![image-20240423203100159](https://s2.loli.net/2024/04/23/QjiskIncw7T9E4G.png)

![image-20240423203212778](https://s2.loli.net/2024/04/23/O1yt4lv8H95XdWn.png)

4.输入Internet地址或网络地址：

```
ftp://10.26.58.111
```

![image-20240423203455064](https://s2.loli.net/2024/04/23/s981hadbKNUWYJr.png)

5.一定记得**取消匿名登录**！！输入用户名“bionet"。

![image-20240423203635552](https://s2.loli.net/2024/04/23/NnB3aCoSYVpLDzy.png)

![image-20240423203732896](https://s2.loli.net/2024/04/23/heH3M2NxyAZXmUf.png)

6.给你这个网络位置起一个名字，这里自己起一个方便自己标识的名字就行，这里我起名叫做bionet-tr。

![image-20240423204219721](https://s2.loli.net/2024/04/23/rXPga2uA43iMG8W.png)

7.输入密码之后，密码详见群内信息，完成。

![image-20240423204302462](https://s2.loli.net/2024/04/23/GUFsW2z6jOCyVxH.png)

8.使用。此时“我的电脑”会出现以下网络连接图标，点击图标进入。此时就可以通过拖拽进行数据上传了。

![image-20240423204424138](https://s2.loli.net/2024/04/23/1DqclYEfFA2Jpia.png)

![image-20240423204739094](https://s2.loli.net/2024/04/23/tnK86qs74ToNxZD.png)

## 上传内容没有及时更新该怎么办？

映射之后，我们打开：

![image-20240423214856217](https://s2.loli.net/2024/04/23/THoBR5P9wdnQmK7.png)

点击刷新即可：

![image-20240423214906566](https://s2.loli.net/2024/04/23/3W4yhu2RpxQgZd7.png)

## 一些使用规则

由于FTP权限管理比较薄弱，各位拥有的权限都比较高，所以请将较大的数据集放入Dataset文件夹：

再强调一遍！请将对应的数据放到Dataset文件夹，按照以下命名规则：

`使用者姓名__数据集名__版本`。举个例子：

![image-20240423220546039](https://s2.loli.net/2024/04/23/ySpLnWN9D3VewUu.png)

**需要强调的是，v是小写，`_`是英文下划线，一个即可。**

**创建之后没有及时显示，请刷新即可**。

私人文件请放入上一层，以自己名字命名的文件夹下，请创建自己的文件夹如图所示：

<img src="https://s2.loli.net/2024/04/23/x59UIvJ81hN7HmY.png" alt="image-20240423220213345" style="zoom:67%;" />

**创建之后没有及时显示，请刷新即可**。

## 上传之后如何在服务器上使用

该NAS服务器映射在原来服务器的/home/Data_NAS/路径下。进入该路径即可看到所有上传的数据。

<img src="https://s2.loli.net/2024/04/23/ZcW8fD2J1u6gib5.png" alt="image-20240423205229731" style="zoom:80%;" />

<img src="https://s2.loli.net/2024/04/23/BARezykG3fYimQ2.png" alt="image-20240423205433620" style="zoom:80%;" />