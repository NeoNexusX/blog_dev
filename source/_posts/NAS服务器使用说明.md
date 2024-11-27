---
title: NAS服务器使用说明
categories:
  - Bionet
tags:
  - Bionet
  - NAS
date: "2024/09/29 20:46:25"
---



# NAS服务器使用说明

<img src="https://s2.loli.net/2024/09/29/F4JzXvoRg5kuWin.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727547916644" style="zoom:50%;" />

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

## 最佳实践

### 一些基础知识

下图展示了存储配置的全部架构：

```mermaid
graph LR;
    A["容器(最高权限)"] 
    B["服务器主机A"]
    F["服务器主机B(待建设)"]
    C["NAS(网络附加存储)"]
    D["你的本地电脑"]
    E["外部网盘"]
    
    
    A--"Datasets文件夹依赖于"--->C
    A--"运行依赖于"--->B
    A--"运行依赖于"--->F
    B--"Datasets文件夹依赖于"--->C
    F--"Datasets文件夹依赖于"--->C
    A--"NCZone、R_Share、P_Share文件夹直接存在于"--->B
    D =="Bionet(Datasets)文件夹依赖于"==>C
    D--"连接运行"--->A
    E--"数据"-->D
   
```

在这里再次对主机（服务器主机）上存在的文件解释一下：

主机上的文件存储由这样的硬盘空间组成的,分为高速、中速、低速区。顾名思义，每个区域速度不同，速度不同的同时稳定性也存在一定的差异，稳定性和速度正好相反：

`Data_NAS(Datasets) > Samsung 860evo 512G  > GLOWAY YCQ4TNVMe-M.2` 

其中每一条格式如下：[分区名、挂载路径、分区大小]

```mermaid
graph TB;
  subgraph HighSpeed
  nvme0n1("nvme0n1:GLOWAY YCQ4TNVMe-M.2")
  nvme0n1 --> nvme0n1p1("nvme0n1p1,[/home],2.5T")
  nvme0n1 --> nvme0n1p2("nvme0n1p2,[/],1T")
  nvme0n1 --> nvme0n1p3("nvme0n1p3,[swap],256G")
  nvme0n1 --> nvme0n1p4("nvme0n1p4,[/var],128G")
  nvme0n1 --> nvme0n1p5("nvme0n1p5,[/opt],128G")
  nvme0n1 --> nvme0n1p6("nvme0n1p6,[/tmp],50G")
  end
  HighSpeed --> MiddleSpeed
  subgraph MiddleSpeed
  sda("sda:Samsung 860evo 512G")
  sda --> sda1["sda1,[/boot/efi],500MB"]
  sda --> sda2["sda2,[/boot],1GB"]
  sda --> empty["docker lvm[/var/lib/docker] 400G"]
  end
  MiddleSpeed --> LowSpeed
  subgraph LowSpeed
  NAS --> NFS1(NFS,/home/Data_NAS,16T)
  NAS --> NFS2(NFS,/home/Datasets,16T)
  end
```

这其中包含了共享文件夹和用户文件夹，如图共享文件夹使用红框标注，用来传递文件

![image-20240513230049068](https://s2.loli.net/2024/05/13/hMrBjZOIpcC5Fgv.png)

一共包含五个文件夹：

- Datasets文件夹是用来放置公用数据集的，其命名需要遵循以下方式：`上传者_数据集名称_上传日期`。
  - 此文件夹使用详见：[NAS服务器使用说明](http://10.26.58.109/2024/09/29/NAS%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E/)
- Data_NAS是直接连接到NAS的文件夹，其目录内容与Datasets保持一致，但是二者物理通道不同（挂载在不同的IP），使用起来是一模一样的。
- PastData文件夹是过去硬盘中的所有数据，在三个月后将会自动删除，各位及时使用。【现在已经删除】
- NCZone文件夹是没有做任何保护的文件夹，用户随时可写可删除，使用的时候需要小心删除，并避免写冲突问题（多人对同一个文件修改），其不被多个服务器所共享，**只保存在No1或者No2服务器上**。
- Some_scripts文件夹使用分享一个shell脚本的，用来管理系统方便的，使用需要管理员权限。

- SoftWares文件夹比较特殊：

![image-20240401205431946](https://s2.loli.net/2024/04/01/dXxH8ARVvPUEf6r.png)

里面包含了P_Share和R_Share,分别挂载到了对应的容器用来分享文件，容器使用的时候只可读，部分情况下可以写入，如果报错则说明无法写入（处于权限和安全考虑这样设置的），需要通过桌面用户来写入文件。

P代指python容器，R代指R语言容器的存储空间。

### 最佳实践

简化后的路径当中红色的路径为推荐的上传数据集的方式：

<img src="https://s2.loli.net/2024/10/21/mZun6qNW3tvPTKB.png" alt="image-20241021232145477" style="zoom:80%;" />

代码等数据集合比较小的可以直接上传：

![image-20241021234950722](https://s2.loli.net/2024/10/21/y6vCgVmNFjOHD4T.png)

对于R容器，建议直接使用R_Share或者NCZone,可以考虑如下路径：

![image-20241021235340468](https://s2.loli.net/2024/10/21/naqj2KiNpMvyzoP.png)

Matlab使用路径同其他。