---
title: BionetServer-No1使用说明-基础篇
categories:
  - Bionet
tags:
  - Bionet
  - ops
date: "2026/03/18 16:31:36"
---

# BionetServer-No1使用说明-基础篇



<img src="https://s2.loli.net/2024/09/29/vNKRfOVDTng7wdL.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727547378732" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />


Date: 2026.3.18

Authors：NeoNeuxs, dlc

<!-- toc -->

[TOC]

# 系统信息篇

## 系统硬盘分区

目前系统是由这样的硬盘空间组成的,分为高速、中速、低速区：

顾名思义，每个区域速度不同，速度不同的同时稳定性也存在一定的差异，稳定性和速度正好相反：

`Data_NAS(Datasets) > Samsung 860evo 512G  > GLOWAY YCQ4TNVMe-M.2` 

### 课题组服务器网络拓扑图

![](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/69b8dc83df27f3bc58070a6d.png_neo)

## 公用文件夹指南

`/home`文件夹：

<img src="https://s2.loli.net/2024/04/01/fm4gUGQCToIbkv8.png" alt="image-20240401202923240" style="zoom:80%;" />

这其中包含了共享文件夹和用户文件夹，如图共享文件夹使用红框标注，用来传递文件

![](https://pic1.imgdb.cn/item/69b8dcb7df27f3bc58070b9a.png)

一共包含两个文件夹：

- Datasets文件夹是用来放置公用数据集的，其命名需要遵循以下方式：`上传者_数据集名称_上传日期`。
  - 此文件夹使用详见：[NAS服务器使用说明](http://10.26.58.109/2024/09/29/NAS%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E/)
- SoftWares文件夹比较特殊：安装了matlab等软件

## 网络端口信息

**端口的使用主要集中在容器。可以通过portainer来查看，portainer如何使用详见docker篇。**

## 软件安装信息

Anaconda：/home/anaconda

Matlab：/home/SoftWares/Matlab

Dcoker：/默认路径

Jetbrains IDEs：/home/SoftWares/Jetbrain

## 基于UI的方式远程连接服务器

首先我们要确认是否连接到了校园网，在系统命令行上使用命令：

```bash
ping 10.26.58.61
```

结果如下则证明你在校园网内。

<img src="https://s2.loli.net/2024/05/13/Yts7QOiU1ymlfrS.png" alt="image-20240513224911484" style="zoom:67%;" />

打开windos远程连接，在状态栏中搜索远程连接，对于Windows系统，家庭版往往没有此功能，需要升级至专业版，具体升级方法可以去某宝买一个激活码，价格在10元左右。询问店家如何激活升级。

<img src="https://s2.loli.net/2024/05/13/yiS3oGNWZaUFMbz.png" alt="image-20240513224939223" style="zoom:80%;" />

打开远程桌面输入远程地址，打开选项：

<img src="https://s2.loli.net/2024/05/13/igJMIEtfhBOQC4Y.png" alt="image-20240513225154369" style="zoom:67%;" />

打开选项之后，输入链接地址：10.26.58.61

然后点击显示选项，参照我这里修改：

<img src="https://s2.loli.net/2024/05/13/sFaqp1WPLRJNeOx.png" alt="image-20240513225353292" style="zoom:67%;" />

然后点击编辑，修改成你的用户名和密码，连接即可。

<img src="https://s2.loli.net/2024/05/13/NhkFSiZEv8rxVgX.png" alt="image-20240513225650039" style="zoom:67%;" />

输入你的用户名和密码：

<img src="https://s2.loli.net/2024/05/13/AwEjJ9RDTV1qifg.png" alt="image-20240513225551955" style="zoom:67%;" />

连接后进入桌面，即可开始使用，如遇到剪贴板不能互相传输、小文件不能传输，请关闭连接重开即可，文件传输依赖于NAS可以参考一下NAS篇介绍。

# 用户篇

## 桌面服务

### Xfce桌面

<img src="https://s2.loli.net/2024/03/24/DGACKf3BFswoix1.png" alt="image-20240324173242318" style="zoom:80%;" />

## 如何正确退出远程连接？

开启远程链接之后如果按照以下操作，会话将会保持运行，占用资源，正确退出方法如下：

<img src="https://s2.loli.net/2024/03/25/mPSAMvcUsreYgQ7.png" alt="image-20240325115418950" style="zoom: 80%;" />

## 如何关闭锁屏功能

在这里找到设置：

<img src="https://s2.loli.net/2024/04/20/Vv6n4ItDzgpUXsr.png" alt="image-20240420190046154" style="zoom:80%;" />

<img src="https://s2.loli.net/2024/04/20/LP43Aq8mX1I2ecb.png" alt="image-20240420190108244" style="zoom:67%;" />

<img src="https://s2.loli.net/2024/04/20/uIiRbpaY6Z2OBhx.png" alt="image-20240420190123305" style="zoom:67%;" />

关闭即可。

## 如何配置中文输入法

打开fctix 5：

![image-20240326205930890](https://s2.loli.net/2024/03/26/HzeqfrZxGpQOcmj.png)

这里会提示运行fctix 5：

![image-20240326210030357](https://s2.loli.net/2024/03/26/Eeza317wogSMcli.png)

在这里找到拼音：

<img src="https://s2.loli.net/2024/03/26/PX9Gdk5ElF8VI4C.png" alt="image-20240326210056577" style="zoom: 80%;" />

点击添加并应用：

<img src="https://s2.loli.net/2024/03/26/8vgad9fEVwjA5yW.png" alt="image-20240326210139494" style="zoom:80%;" />

找到语言设置：

![image-20240326210306567](https://s2.loli.net/2024/03/26/cDq7MzyRkbVBadi.png)

更改设置如下，并登出，重新启动之后

![image-20240326210336015](https://s2.loli.net/2024/03/26/bCHiPtZSow3vcmu.png)

<img src="https://s2.loli.net/2024/03/26/tIrZ4owaCkPXvQc.png" alt="image-20240326210413055" style="zoom:80%;" />

可以愉快使用：

**注意输入法切换要使用ctrl+空格键**

![image-20240326210456544](https://s2.loli.net/2024/03/26/nIgFTsCrmcxbMzt.png)

## 基本的开发工具

使用UI开发的基本工具都在：

<img src="https://s2.loli.net/2024/05/13/bRqM8GFJT9Hjs3C.png" alt="image-20240513230203466" style="zoom:67%;" />

PyCharm使用的介绍这里不赘述，网上有很多，需要提醒的是选择Python Inter（python解释器）的时候

## Anaconda使用说明

**禁止事项：严禁在用户个人目录下私自安装Anaconda

## 服务器端的Anaconda如何使用？

在命令行中使用命令：

```bash
 conda activate base
 conda env list # 看所有的conda环境有哪些
```

![image-20240513230400329](https://s2.loli.net/2024/05/13/INaD3GAgvVC8TPy.png)

注意base环境不可以使用，请自己建立环境：

```bash
conda create -n env_name python=3.10
# 激活test0环境
conda activate test0
python --version # 可以激活后看python版本，验证一下
```

其中env_name为你想创建的环境的名字，python后边为创建的版本。

## Matlab使用说明

**禁止事项：严禁在用户个人目录下私自安装Matlab**

matlab在路径：

```bash
/home/SoftWares/MATLAB/bin
```

在目录下右键打开命令行，运行matlab即可

```
./matlab
```

或者快捷方式在：

<img src="https://s2.loli.net/2024/06/07/TKCeJRObo98VyXQ.png" alt="image-20240607222500101" style="zoom:80%;" />

<img src="https://s2.loli.net/2024/06/07/vlaz2VtP8ZRCWsw.png" alt="image-20240607222559328" style="zoom:67%;" />

**此命令行为matlab运行所使用，不要关闭。**开启后登陆个人matlab账号即可使用，安装的matlab皆为正版，需要根据学校引导来注册正版账号，引导内容在：https://zhengban.xmu.edu.cn/matlab.html，查看个人用户部分介绍，遵循指南。

#### 报错处理

<img src="https://s2.loli.net/2024/09/02/a2XJilmTOsoyQdU.png" alt="image-20240902193011563" style="zoom:50%;" />

根据matlab官方指南，kill掉对应的进程即可，使用命令：

```
ps -fU 用户名 | grep MathWorksServiceHost
```

![image-20240902193935209](https://s2.loli.net/2024/09/02/7Wz195ZHTEn3hwD.png)

使用`kill -9  1220267`对应的进程号码即可。

## 代理使用：

```bash
# 为终端设置临时代理
export http_proxy=http://代理服务器IP:端口
export https_proxy=http://代理服务器IP:端口

# 为 Git 设置全局代理
git config --global http.proxy http://代理服务器IP:端口
git config --global https.proxy http://代理服务器IP:端口
```

## R语言使用指南

为了方便R语言版本的切换，R语言通过浏览器访问，地址`10.26.58.61:8787`，详见[RStudio](https://pleinelune-r.github.io/2025/08/05/R-Studio%E5%AE%B9%E5%99%A8%E4%BD%BF%E7%94%A8%E5%8F%8AMSI%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86%E6%B5%81%E7%A8%8B/#%E4%BA%8C%E3%80%81RStudio%E6%A0%87%E5%87%86%E8%BF%9E%E6%8E%A5)
