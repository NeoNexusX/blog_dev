---
title:  Android开发笔记——快速入门（系统架构和Android Studio安装）
categories:
  - Android
tags:
  - Android
excerpt: "系统架构和Android Studio安装"
---

# Android开发笔记——快速入门（系统架构和Android Studio安装）

<img src="https://s2.loli.net/2024/10/04/uIcZWBhgfYLxHrX.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727982662332" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]



## Android的系统架构

书上往往介绍为4层实际上谷歌官方将其分为如下：

<img src="https://s2.loli.net/2024/10/04/PeXupSm1EVNTIci.png" alt="android-stack_2x" style="zoom:80%;" />

### Linux 内核 

Android 平台的基础是 Linux 内核。例如，[Android Runtime (ART)](https://developer.android.com/guide/platform#art) 依靠 Linux 内核来执行底层功能，例如线程和低层内存管理。使用 Linux 内核可让 Android 利用[主要安全功能](https://source.android.com/security/overview/kernel-security.html)，并且允许设备制造商为著名的内核开发硬件驱动程序。

### 硬件抽象层 (HAL) 

[硬件抽象层 (HAL)](https://source.android.com/devices/architecture/hal-types) 提供标准界面，向更高级别的 [Java API 框架](https://developer.android.com/guide/platform#api-framework)显示设备硬件功能。HAL 包含多个库模块，其中每个模块都为特定类型的硬件组件实现一个界面，例如[相机](https://source.android.com/devices/camera/index.html)或[蓝牙](https://source.android.com/devices/bluetooth.html)模块。当框架 API 要求访问设备硬件时，Android 系统将为该硬件组件加载库模块。

### Android Runtime 

对于运行 Android 5.0（API 级别 21）或更高版本的设备，每个应用都在其自己的进程中运行，并且有其自己的 [Android Runtime (ART)](https://source.android.com/devices/tech/dalvik/index.html) 实例。ART 编写为通过执行 DEX 文件在低内存设备上运行多个虚拟机，DEX 文件是一种专为 Android 设计的字节码格式，经过优化，使用的内存很少。编译工具链（例如 [Jack](https://source.android.com/source/jack.html)）将 Java 源代码编译为 DEX 字节码，使其可在 Android 平台上运行。

### 原生 C/C++ 库 

许多核心 Android 系统组件和服务（例如 ART 和 HAL）构建自原生代码，需要以 C 和 C++ 编写的原生库。Android 平台提供 Java 框架 API 以向应用显示其中部分原生库的功能。例如，您可以通过 Android 框架的 [Java OpenGL API](https://developer.android.com/reference/android/opengl/package-summary) 访问 [OpenGL ES](https://developer.android.com/guide/topics/graphics/opengl)，以支持在应用中绘制和操作 2D 和 3D 图形。如果开发的是需要 C 或 C++ 代码的应用，可以使用 [Android NDK](https://developer.android.com/ndk) 直接从原生代码访问某些[原生平台库](https://developer.android.com/ndk/guides/stable_apis)。

### Java API 框架 

您可通过以 Java 语言编写的 API 使用 Android OS 的整个功能集。这些 API 形成创建 Android 应用所需的构建块，它们可简化核心模块化系统组件和服务的重复使用，包括以下组件和服务：

- 丰富、可扩展的[视图系统](https://developer.android.com/guide/topics/ui/overview)，可用以构建应用的 UI，包括列表、网格、文本框、按钮甚至可嵌入的网络浏览器
- [资源管理器](https://developer.android.com/guide/topics/resources/overview)，用于访问非代码资源，例如本地化的字符串、图形和布局文件
- [通知管理器](https://developer.android.com/guide/topics/ui/notifiers/notifications)，可让所有应用在状态栏中显示自定义提醒
- [Activity 管理器](https://developer.android.com/guide/components/activities)，用于管理应用的生命周期，提供常见的[导航返回栈](https://developer.android.com/guide/components/tasks-and-back-stack)
- [内容提供程序](https://developer.android.com/guide/topics/providers/content-providers)，可让应用访问其他应用（例如“联系人”应用）中的数据或者共享其自己的数据

开发者可以完全访问 Android 系统应用使用的[框架 API](https://developer.android.com/reference/packages)。

### 系统应用 

Android 随附一套用于电子邮件、短信、日历、互联网浏览和联系人等的核心应用。平台随附的应用与用户可以选择安装的应用一样，没有特殊状态。因此第三方应用可成为用户的默认网络浏览器、短信 Messenger 甚至默认键盘（有一些例外，例如系统的“设置”应用）。

## Android的四大组件和SQLite数据库

#### 四大组件

四大组件分别是**Activity**、**Service**、**BroadcastReciver**、**ContentProvider**。

**Activity**是应用程序的门面，就是外观设计的必需品，凡是在应用中你看得到的东西都是在**Activity**中实现的。

**Service**是应用在后台的运行，即使退出了应用其也可以在继续运行。

**BroadcastReciver**允许接受和发送广播消息，这里指的是电话短信等通信。

**ContentProvider**是用来向不同的应用程序提供数据共享的组件，通过**ContentProvider**你就可以获取通讯录等。

#### SQLite数据库

系统自带了轻量级的、运算速度极快的嵌入式关系型数据库。支持标准的SQL语法，还可以通过封装好的API进行操作。读取和存储数据变得非常方便。

## 开发环境的搭建：

需要以下软件：

- **Jetbrains Toolbox**

- **JDK**
- **Android SDK 由谷歌提供的开发工具包，在开发应用程序时，我们需要引入该工具来使用相关API。**
- **Android Studio**

强烈推荐使用**Jetbrains Toolbox**为安装，迁移等提供了极大的方便，特别方便管理**IDEA**等IDE。

[Jetbrains Toolbox下载地址](https://www.jetbrains.com/toolbox-app/)

安装也十分简单直接默认安装即可。安装后在windos状态栏左下角可以看见图标，单击打开。

这里我已经安装了所以在上边显示，直接在可用里面找到点击安装即可。

![1](https://s2.loli.net/2024/10/04/cf86kF7bMd5zZ4H.png)



如果需要配置一些信息可以打开上图的三个点进行配置：

![2](https://s2.loli.net/2024/10/04/uANSjiX9kCEcfdR.png)

选择Do not即可。

![3](https://s2.loli.net/2024/10/04/Sg2Ip9nPRVtiDWl.png)

点击ok以后会显示没有SDK安装的界面，这里选择Cancel。

![4](https://s2.loli.net/2024/10/04/TN8cLFEw5yRQxBA.png)

开启安装向导：

![5](https://s2.loli.net/2024/10/04/PdlqRfatXsUJTK3.png)

这里选择Custom安装因为这样可以指定安装位置，节省c盘空间。

![6](https://s2.loli.net/2024/10/04/BQe9EcvIdAbPzi1.png)

选择已安装的JDK的位置（这里提示一下，如果你是通过IDEA安装的JDK可能不显示因为没有添加到环境变量里面，可以打开cmd测试一下）。

![7](https://s2.loli.net/2024/10/04/HEDCuKyhi5U2G3n.png)

打开cmd测试一下JDK：

输入

```shell
java -version
```

![25](https://s2.loli.net/2024/10/04/s5MJ4EUC6OYq8Wk.png)

指定好JDK后选择SDK安装位置：

![8](https://s2.loli.net/2024/10/04/6amWvjGDR1dThC4.png)

这里需要验证一下设置是否正确（和你指定的位置是否一样）。

![9](https://s2.loli.net/2024/10/04/7Mb9w8aAyoFqERO.png)

这里时需要同意每一项的License要求点击Accept即可。

![10](https://s2.loli.net/2024/10/04/fqWKVbiNLgkjazM.png)

这里需要等待下载SDK，如果下载失败，你就需要一个魔法来辅助你下载。

![11](https://s2.loli.net/2024/10/04/zh8K6ytu1aPOXNR.png)

下载完成后看到这个界面就可以退出。

![12](https://s2.loli.net/2024/10/04/jJEIea3G8WZ7fzH.png)

点击创建一个新的Project。这里选择一个空的Activity。一路默认next下去即可。

![13](https://s2.loli.net/2024/10/04/LY8Zx1u6JfOhylF.png)

等待初始化完成，右下角显示下载进度。

![14](https://s2.loli.net/2024/10/04/7isWdvbyLmNUoYp.png)

完成以后首先点击设备管理器，然后选择创建一个设备，这里就选择Pixel 5来进行模拟。

![15](https://s2.loli.net/2024/10/04/ENVBKL8zbMc1RXH.png)

next后让你选择API等级，此处的API等级应该指的是Java API 框架 的等级包括了**Activity**等。

![16](https://s2.loli.net/2024/10/04/cBE21uHnMYiylb6.png)

此处显示了不同等级的API兼容性。

![17](https://s2.loli.net/2024/10/04/6Ly7pTiO8W1NqGs.png)

点击Download会进入API下载器开始下载。

![18](https://s2.loli.net/2024/10/04/pZCfj7OcNGeuqgz.png)

当然需要一些魔法来实现下载，完成后后点击finish。

![19](https://s2.loli.net/2024/10/04/DrqV4cxHheGXNw6.png)

下载完成后会进入虚拟配置阶段，此处使用的时刚才选择的pixel5作为虚拟化设备，这里推荐使用硬件解码来实现，如红框部分，选择完成后点击finish。

![20](https://s2.loli.net/2024/10/04/CLXEMW1ratOkgNn.png)

会看见虚拟机启动的界面。

![21](https://s2.loli.net/2024/10/04/mywoYSA1MXCIakK.png)

点击右上角运行项目等待完成编译，手机自动运行项目启动。

![22](https://s2.loli.net/2024/10/04/7luwvmc2VtdxyYE.png)