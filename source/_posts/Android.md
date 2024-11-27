---
title: Android开发笔记——快速入门（第一个Android项目）
categories:
  -  Android
tags:
  -  Android
excerpt: "第一个Android项目"
date: 2024/10/22 00:00:00
---

# Android开发笔记——快速入门（第一个Android项目）

<img src="https://s2.loli.net/2024/09/29/MrYRTUN64jDSZ5c.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727615445573" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

[TOC]

<!-- toc -->



## 第一个Android项目

首先你需要安装**Android Studio**来进行开发，详细安装过程可以参考：[Android开发笔记——快速入门（壹)](https://blog.csdn.net/qq_20540901/article/details/123426867)

根据教程创建一个基于**Kotlin**的项目：

选择新建一个项目以后，选择**Empty Activity**，点击next。

![13](https://s2.loli.net/2024/09/29/o35u4nGSqmU6cgl.png)

接下来配置项目一些属性，

**Name**代表了项目的名称；

**Package name**表示项目的包名，在**Android**中项目的包名可以用来区分不同的应用程序这个一定要保持唯一性，通常会根据**Name**来自动设定；

**Save Location**表示代码存放的位置，看个人喜好设定；

**Language**选择**Kotlin**；

**Minimum SDK：**SDK最小支持的 JAVA API level等级；

最下边的选项是**Android support library**是否支持，目前谷歌已经几乎淘汰了**Android support library**

，替换成了**Android X**。详细了解可以看：[Android X](https://blog.csdn.net/guolin_blog/article/details/97142065)。

![2](https://s2.loli.net/2024/09/29/nJ3FsBmuyVHqSi7.png)

点击Finish后项目会进行预加载会进行一段时间。

## 创建一个Android模拟器

在环境搭建部分已经安装了模拟器这里直接运行一个就ok，

![3](https://s2.loli.net/2024/09/29/WEuiCwO3aStKB9L.png)

![4](https://s2.loli.net/2024/09/29/y4iVG7sbzC6RqwX.png)

![5](https://s2.loli.net/2024/09/29/vgykp8BzCdW9wNs.png)

## 项目内容分析

![6](https://s2.loli.net/2024/09/29/nSVXrWzBxKtbNaQ.png)

在一般情况下，默认创建的**Android**项目并不是磁盘上的结构，这是因为AS替我们隐藏了一些不必要的信息，但是为了详细了解还是打开**Project模式**看一看：

![7](https://s2.loli.net/2024/09/29/YCda9rR82G75yJx.png)

**.idea**和.**gradled** 是由**AS**所自动产生的gradle配置工具和idea所需要的配置信息，这里不需要深入了解。

**app**就是我们开发所最关心的目录，打开可以看到：

![8](https://s2.loli.net/2024/09/29/LeTp1IWEGAnmgKk.png)

- 里面分别还有一个**build文件夹**，这个是存放编译app代码后的地方；
- 当你需要使用外部的**Jar**包的时候将外部的**jar**包放入**libs文件夹**，就可以在编译的时候实现自动引用；
- **src文件夹**就是存放的我们需要写的代码了；
- 在**src**文件夹的**res**目录下存放着整个项目需要的所有资源，包括图片、布局、字符等，后边使用会详细讲；
- 在**src文件夹**的**java**目录下存放着整个项目的代码；
- 在**src文件夹**的**AndroidMainifest.xml**,这个是整个Android项目的配置文件，需要使用的组件都需要在这里注册，并给应用程序添加权限声明。

回到主目录

**gradle文件夹**包含了gradle wrapper，gradle wrapper是针对gradle的一种配置工具，[具体可以看这里](https://blog.csdn.net/sinat_31311947/article/details/81084689)；

**build.gradle**是全局的gradle构建脚本，稍后会详细分析；

**gradle.properties**是针对gradle构建的全局配置文件，包含了需要配置的构建属性，更改这个会影响全局gradle构建脚本 [具体可以参考这里；](https://blog.csdn.net/u013553529/article/details/55011602)

**gradle.bat**是在Windos环境下使用的命令行；

**settings.gradle**这个文件指定的是项目所有引用的模块，一般只有app模块，通常情况下是自动完成引用的；



## AndroidMainifest.xml的分析

![9](https://s2.loli.net/2024/09/29/z3uvANDVnyU8Zr7.png)



这个xml看起来一大堆的，看起来很麻烦，这波就来一个庖丁解牛分析：

先回忆一下，**AndroidMainifest**是整个安卓项目的配置文件，所以里面的内容一定是和项目的各个属性相关的，最外层的标签你可以猜到是用来表示这个应用的最外层，标签以内都是这个应用的属性，大眼一扫你可以看到一个很熟悉的关键字**activity**，这段代码实际上是为应用注册了一个名字为**MainActivity**的组件，在**activity**标签里面还有一个**intent-filter**标签是过滤器标签节点，通过**Action**属性和**category**分别实现了把**Activity**设为主**Activity**和启动**Activity**。，在手机上点击图标首先启动的就是这个**Activity**。

看回到外层，可以看到许多这样的形式：`android:icon="@mipmap/ic_launcher`，这些是干什么的呢？在这里就要回想一个东西：**res文件夹**，通过英文名称你也能猜到这表示的是某个图标，而后面的就是图标存放的位置，事实的确如此，打开res文件夹可以看到有着相同路径的内容，这里的@语法就是引用的意思：

![10](https://s2.loli.net/2024/09/29/6M21mqdNGWDh3Yz.png)

看到图片你会想到一个问题，为什么有这么多Mipmap开头的文件夹？其实后缀是针对不同的分辨率，现在主流的分辨率还是xxhdpi，你如果有合适的图标就可放进去。

## build.gradle的分析

gradle是一种新型构建工具，不同于传统的XML，使用基于Groovy的领域特定语言（目前还不太懂，挖个坑）。

其实在app目录下和项目根目录下都有一个build.gradle脚本，我们先来看看最外层的脚本：

```groovy
// Top-level build file where you can add configuration options common to all sub-projects/modules.
plugins {
    id 'com.android.application' version '7.1.2' apply false
    id 'com.android.library' version '7.1.2' apply false
    id 'org.jetbrains.kotlin.android' version '1.5.30' apply false
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

和老版小雪狐的不太相同的是小蜜蜂版本出现了许多位置变化，可以参考：[Gradle新版变化](https://blog.csdn.net/sinat_38167329/article/details/123175556)。

参考以上链接：

工程的build.gradle的**dependencies**修改为plugins，替代了引用原来的Gradle版本。

```groovy
plugins {
    id 'com.android.application' version '7.1.2' apply false
    id 'com.android.library' version '7.1.2' apply false
    id 'org.jetbrains.kotlin.android' version '1.5.30' apply false
}
```

功能位置迁移，原来在工程build.gradle的**buildscript**和**allprojects**移动至**setting.gradle**并改名为**pluginManagement** 和**dependencyResolutionManagement**。里面的东西依旧可以按照原来的copy过来。

```groovy
pluginManagement {
    repositories 
    {
        gradlePluginPortal()
        google()
        mavenCentral()
    }
}
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories
    {
        google()
        mavenCentral()
    }
}
rootProject.name = "My Application"
include ':app'
```

两个repositories分别中的的   `google(）  mavenCentral()`分别对应了两个官方仓库，通过这两个配置可以在项目中引用这两个仓库中的所有依赖。

### build.gradle的模块配置

```groovy
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}

android {
    compileSdk 32// 编译时用的SDK版本

    defaultConfig {
        applicationId "com.example.myapplication"//应用标识名称，这里默认使用应用名字，系统根据这个区分不同应用，应用的唯一标识
        minSdk 21//最小SDK兼容版本
        targetSdk 32//测试使用的sdk版本
        versionCode 1//代码的版本
        versionName "1.0"
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false//是否对代码进行混淆
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'//混淆规则
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8//编译兼容java版本
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'//kotlin运行的目标jvm版本
    }
    buildFeatures {
        viewBinding true
    }
}

dependencies {

    implementation 'androidx.core:core-ktx:1.7.0'
    implementation 'androidx.appcompat:appcompat:1.3.0'
    implementation 'com.google.android.material:material:1.4.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.0.4'
    implementation 'androidx.navigation:navigation-fragment-ktx:2.3.5'
    implementation 'androidx.navigation:navigation-ui-ktx:2.3.5'
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.3'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.4.0'
}
```

接下来就需要一点点分析了，开头还是针对插件的引用，`'com.android.application'`代表这是一个应哟个程序模块，有时你可以建立一个`'com.android.library'`表示是库模块。而这最大区别在于，库模块依附于其他应用程序，而应用程序可以独立于运行。具体内容要了解可以看一下注释。

dependecies闭包是需要详细说一下的，

如需向项目添加依赖项，请在 `build.gradle` 文件的 `dependencies` 代码块中指定依赖项配置，如 `implementation`。

例如，应用模块的以下 `build.gradle` 文件包含三种不同类型的依赖项：

- **本地库模块依赖项**

```groovy
implementation project(':mylibrary')
```

这声明了对一个名为“mylibrary”（此名称必须与在您的 [`settings.gradle`](https://docs.gradle.org/current/dsl/org.gradle.api.initialization.Settings.html) 文件中使用 `include:` 定义的库名称相符）的 [Android 库模块](https://developer.android.google.cn/studio/projects/android-library)的依赖关系。在构建您的应用时，构建系统会编译该库模块，并将生成的编译内容打包到 APK 中。目前还没有用到用到时具体再说。

- **本地二进制文件依赖项**

```groovy
implementation fileTree(dir: 'libs', include: ['*.jar'])
```

Gradle 声明了对项目的 `module_name/libs/` 目录中 JAR 文件的依赖关系（因为 Gradle 会读取 `build.gradle` 文件的相对路径）。

- **远程二进制文件依赖项**

```groovy
implementation 'com.example.android:app-magic:12.3'
```

这声明了对“com.example.android”命名空间组内的 12.3 版“app-magic”库的依赖关系。

gradle在构建的时候会检查一下本低是否有这个库的缓存，如果没有会自动联网下载，在添加到目录中