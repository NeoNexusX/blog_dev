---
title: NAS服务器使用说明
categories:
  - Bionet
tags:
  - Bionet
  - NAS
date: "2026/03/18 16:59:25"
---



# NAS服务器使用说明

<img src="https://s2.loli.net/2024/09/29/F4JzXvoRg5kuWin.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727547916644" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

Version:1.0

Date: 2026.03.18

Authors：Tr，NeoNeuxs，dlq

<!-- toc -->

## 基本的网络要求

网络要求需要在校园网中，在校园网外需要使用VPN来进行上传。上传文件的速度取决于你的链接速度，推荐使用网线上传。

为什么我的网速很慢？打开校园网链接信息，网线连接的用户，一般不用考虑这个问题，此处可以跳过。

<img src="https://s2.loli.net/2024/04/23/CWBdyLkx72UXKna.png" alt="image-20240423215045985" style="zoom:67%;" />

查看连接信息：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/S2b6eO89k7hdFAH.png_neo" alt="image-20240423215142791" style="zoom: 67%;" />

应连接到5HZ频段，2.4Ghz频段速度较低，较慢的情况如下：

<img src="https://s2.loli.net/2024/04/23/8G6U7LedFmag9z4.png" alt="image-20240423215305320" style="zoom: 50%;" />

**72Mbps相当于10M左右的网速，如果上传100GB的数据那就需要100*1000/10 = 10000s，也就是2.78个小时才能完成上传。**所以使用的时候请注意。

如何从2.4Ghz切换到5Ghz？首先电脑支持5Ghz频段才能使用，2.4Ghz频段切换方法如下：

![image-20240423215639861](https://s2.loli.net/2024/04/23/j2pdsZQyIFPi7C8.png)

点击**断开连接**后再次点击连接就能查看所连接的信息是否改变，没有改变的情况下，多次尝试即可。

## 目录结构

第一级目录存放了两个文件夹，分别是我个人的用户文件夹，**公用的数据文件夹Dataset**，一般要求如下：

![](https://pic1.imgdb.cn/item/69ba657498447adc3518dd82.png)

# 如何建立映射来上传内容

## 法一：使用 NFS 挂载 NAS 到本地（推荐）

### windows：

#### 开启 windows 的 NFS 服务（如果是家庭版需要升级到专业版）

1. 打开 **控制面板** > **程序** > **启用或关闭 Windows 功能**。

2. 找到 **NFS 服务**，点击展开。

3. 勾选 **NFS 客户端** 和 **管理工具**。

![](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/69b8df15df27f3bc5807172c.png_neo)

#### 在 Windows 上挂载

1. 创建 `mount_nas.bat` 挂载脚本

   - 新建 `mount_nas.txt` 文档，输入 `mount -o nolock 10.26.58.111:/mnt/Bionet_01/Data/bionet N:`
   - 修改后缀为 `mount_nas.bat`


![](https://pic1.imgdb.cn/item/69b8df2cdf27f3bc580717b3.png)

1. 按Win+R，输入 `shell:startup`，打开windows启动文件夹，将`mount_nas.bat`托进去，之后每次开机自动挂载

![](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/69b8df50df27f3bc580718ae.png_neo)

3. 成功后电脑上会新增一个N盘

![](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/69b8dfbfdf27f3bc58071b65.png_neo)

### MacOS

1. 在 Mac 桌面上，确保你在 **Finder** 中。

2. 按下快捷键 **Command (⌘) + K**，或者在顶部菜单栏选择 **前往 (Go) > 连接服务器 (Connect to Server)**。

3. 在地址栏输入以下格式： `nfs://10.26.58.111/mnt/Bionet_01/Data/bionet` 

点击**连接**即可。

![](https://pic1.imgdb.cn/item/69ba6d0f98447adc3519072f.png)

4. 设置开机自启
5. 点击 Mac 左上角的苹果图标  -> **“系统设置”** -> 左侧找到 **“通用”** -> 右侧点击 **“登录项”**。
6. **拖拽固定：** 把桌面上刚才挂载好的网络磁盘图标，直接用鼠标拖拽到“登录时打开”的列表中。

![](https://pic1.imgdb.cn/item/69ba6d4f98447adc3519073a.png)

![](https://pic1.imgdb.cn/item/69ba6d8f98447adc35190744.png)

#### 注意：文件名不要修改

## 上传内容没有及时更新该怎么办？

映射之后，我们打开：

![image-20240423214856217](https://s2.loli.net/2024/04/23/THoBR5P9wdnQmK7.png)

点击刷新即可：

![image-20240423214906566](https://s2.loli.net/2024/04/23/3W4yhu2RpxQgZd7.png)

## 一些使用规则

各位拥有的权限都比较高，所以请将较大的数据集放入Dataset文件夹：

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
    B["Bionet NO1"]
    F["Bionet NO2"]
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

这其中包含了共享文件夹和用户文件夹，如图共享文件夹使用红框标注，用来传递文件

![](https://pic1.imgdb.cn/item/69b8dcb7df27f3bc58070b9a.png)

一共包含两个文件夹：

- Datasets文件夹是用来放置公用数据集的，其命名需要遵循以下方式：`上传者_数据集名称_上传日期`。
  - 此文件夹使用详见：[NAS服务器使用说明](http://10.26.58.109/2024/09/29/NAS%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E/)
- SoftWares文件夹比较特殊：安装了matlab等软件

### 最佳实践

简化后的路径当中红色的路径为推荐的上传数据集的方式：

<img src="https://s2.loli.net/2024/10/21/mZun6qNW3tvPTKB.png" alt="image-20241021232145477" style="zoom:80%;" />

代码等数据集合比较小的可以直接上传：

![image-20241021234950722](https://s2.loli.net/2024/10/21/y6vCgVmNFjOHD4T.png)
