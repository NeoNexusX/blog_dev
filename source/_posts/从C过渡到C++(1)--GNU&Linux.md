---
title:  从C过渡到C++(1)——GNU/Linux
categories:
  - C/C++
tags:
  - GNU
  - C/C++
  - Linux
date: 2024/09/28 00:00:00
---

# 从C过渡到C++(1)——GNU/Linux

<img src="https://s2.loli.net/2024/09/29/g8NzUVm3C2h9uc1.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727589808872" style="zoom:50%;" />

<!-- toc -->

[TOC]

文中使用软件的云盘下载地址：https://www.aliyundrive.com/s/juMJT9dBZgd。

大多人学习C语言会有这样的经历，就是老师让下载一个IDE，比如VS或者Dev-C++,然后就直接开始写代码了，IDE确实很方便不过这些是在Windows下很舒服，直接打开就可以写，但是随着学习和工作变化，你会发现你不仅会使用到Windows，甚至在大多数生产环境下使用的都是Linux，这就很难受了，linux系统在某些特殊情况你使用的是不带有图行化的界面的发行版Linux（Ubuntu就是带有桌面系统的，有些Debian系统没有），如下图如所示：

![202208120030266.png](https://s2.loli.net/2022/08/12/YjEmXH2BQKz85t3.png)

上图使用的就是不带有图形界面的Ubuntu(WSL2),这样的情况在嵌入式开发中很常见，甚至对于Web开发有些服务器上的Linux也是完全不带有图形界面的，这样就谈不上使用IDE了，在这样的环境下你该如何编写一个C语言程序，并将它编译呢？

为了解决这些问题我们不妨先去了解一下GNU/Linux到底是什么？

## 大名鼎鼎的GNU/Linux

如果你看过稚晖君的一篇文章，是关于STM32开发环境配置，但是不同于以往的IDE，稚晖君使用的是Clion，在配置过程中他下载了一个很特殊的配置软件叫MinGW：

![202208120030488.png](https://s2.loli.net/2022/08/12/AYMByUcNJC8eDbl.png)

你可能会照着做做，但是不理解这些东西都是干什么的？或者说不理解这些东西是为什么而存在的，在理解之前我们不妨了解一下什么是GNU。

### GNU

GNU操作系统起源于[GNU计划](https://zh.m.wikipedia.org/wiki/GNU計劃)，由[理查德·斯托曼](https://zh.m.wikipedia.org/wiki/理查·斯托曼)在[麻省理工学院](https://zh.m.wikipedia.org/wiki/麻省理工學院)人工智能实验室发起，希望发展出一套完整的开放源代码操作系统来取代Unix，计划中的操作系统，名为GNU。

![202208120031439.png](https://s2.loli.net/2022/08/12/jQLouxXq39ZigGR.png)

GNU的商标是一个[牛羚](https://zh.m.wikipedia.org/wiki/牛羚)头。原先版本是Etienne Suvasa所设计，而今比较流行的粗体版本则是Aurelio Heckert所设计。

**GNU**是一个[自由](https://zh.m.wikipedia.org/wiki/自由軟體)的[操作系统](https://zh.m.wikipedia.org/wiki/作業系統)，其内容软件完全以[GPL](https://zh.m.wikipedia.org/wiki/GPL)方式发布。这个操作系统是[GNU计划](https://zh.m.wikipedia.org/wiki/GNU計劃)的主要目标，名称来自GNU's Not Unix!的[递归缩写](https://zh.m.wikipedia.org/wiki/遞迴縮寫)，因为GNU的设计类似[Unix](https://zh.m.wikipedia.org/wiki/Unix)，但它不包含具著作权的Unix代码。

你可能会难以理解，我从来没听说过GNU操作系统，像是Linux，Unix这种都是很有名的，但GNU到底是什么？

1983年9月27日，[理查德·斯托曼](https://zh.m.wikipedia.org/wiki/理查·斯托曼)在 net.unix-wizards 和 net.usoft[新闻组](https://zh.m.wikipedia.org/wiki/新聞群組)中公布这项GNU计划。斯托曼的目标是成立一个完全自由的操作系统，他希望电脑用户是能够“自由使用”的。因为在20世纪60年代和70年代 - 大多数人都能自由学习软件的源代码，自由地与他人分享的软件，可自由修改软件的行为，自由发布的软件的修改后的版本。也就是说早些年软件几乎都是免费的，每个人都可以自由地使用和修改。

1984年1月5日，正式开始开发软件，为了防止这些软件将来可能被主张所有权，影响到自由软件的发展，斯托曼辞去了在实验室的工作。

理查德·斯托曼使用[不兼容分时系统](https://zh.m.wikipedia.org/w/index.php?title=不兼容分時系統&action=edit&redlink=1)（英语：[Incompatible Timesharing System](https://en.wikipedia.org/wiki/Incompatible_Timesharing_System)） (ITS)[[6\]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-intervention-6)（一种早期的操作系统，使用[汇编语言](https://zh.m.wikipedia.org/wiki/汇编语言)撰写，因其所运行的称为 [PDP-10](https://zh.m.wikipedia.org/wiki/PDP-10)的电脑系统架构停止发展而变得过时）的经验，导致了需要一种可移植系统的决定。

因此，会议决定，将开发新的系统，并使用 [C](https://zh.m.wikipedia.org/wiki/C语言) 和[Lisp](https://zh.m.wikipedia.org/wiki/Lisp)作为系统编程语言。[[8\]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-8) 且GNU将与UNIX兼容。[[9\]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-9) 当时，UNIX已经是一个流行的专有操作系统。而Unix的设计是模块化的，所以它可以被逐步分块的实现。

Unix在学术机构和大型企业中得到了广泛的应用，当时的UNIX拥有者[AT&T](https://zh.m.wikipedia.org/wiki/AT%26T)公司以低廉甚至免费的许可将Unix源码授权给学术机构做研究或教学之用，许多机构在此源码基础上加以扩展和改进，形成了所谓的“Unix变种”，这些变种反过来也促进了Unix的发展。

后来[AT&T](https://zh.m.wikipedia.org/wiki/AT%26T)意识到了Unix的商业价值，不再将Unix源码授权给学术机构，并对之前的Unix及其变种声明了著作权权利。BSD在Unix的历史发展中具有相当大的影响力，被很多商业厂家采用，成为很多商用Unix的基础。其不断增大的影响力终于引起了AT&T的关注，于是开始了一场持久的著作权官司，由此人们意识到我们需要一个完全免费的操作系统。

1984年，开始发展编辑器[Emacs](https://zh.m.wikipedia.org/wiki/Emacs)等软件。1985年，发表[GNU宣言](https://zh.m.wikipedia.org/wiki/GNU宣言)。1989年，发表[GNU通用公共许可协议](https://zh.m.wikipedia.org/zh-cn/GNU_General_Public_License)。这个公共开源许可协议，你如果开源过项目一定见过，他叫GPL协议，GPL是[自由软件](https://zh.m.wikipedia.org/wiki/自由軟件)和[开源软件](https://zh.m.wikipedia.org/wiki/开源软件)的最流行许可证[[18\]](https://zh.m.wikipedia.org/zh-cn/GNU通用公共许可证#cite_note-18)。到2004年4月，GPL已占[Freshmeat](https://zh.m.wikipedia.org/w/index.php?title=Freshmeat&action=edit&redlink=1&variant=zh-cn)（英语：[Freshmeat](https://en.wikipedia.org/wiki/Freshmeat)）上所列的自由软件的约75%，[SourceForge](https://zh.m.wikipedia.org/wiki/SourceForge)的约68%。类似的，2001年一项关于[Red Hat Linux](https://zh.m.wikipedia.org/wiki/Red_Hat_Linux) 7.1的调查显示一般的代码都以GPL发布。著名的GPL自由软件包括EMACS，[Linux](https://zh.m.wikipedia.org/wiki/Linux)核心（并非所有[Linux发行版](https://zh.m.wikipedia.org/wiki/Linux發行版)的核心都是开源的）和[GCC](https://zh.m.wikipedia.org/wiki/GCC)。

我们回到GNU的操作系统来，因为许多必要的软件需要从零开始写起来，但是有很多已经存在的第三方组件如 [TeX](https://zh.m.wikipedia.org/wiki/TeX)、[X Window System](https://zh.m.wikipedia.org/wiki/X_Window_System)[[4\]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-internethist-4)和[Mach](https://zh.m.wikipedia.org/wiki/Mach)微内核等等都可以进行利用，其中GNU最关键系统内核，是在[GNU Mach](https://zh.m.wikipedia.org/zh-cn/GNU_Mach)[微内核](https://zh.m.wikipedia.org/wiki/微內核)基础上进行开发，**理查德·马修·斯托曼**认为可以借此加速操作系统的开发，但因为一直不确定[卡内基梅隆大学](https://zh.m.wikipedia.org/wiki/卡内基梅隆大学)何时要将核心源代码发布，造成计划延宕三年。他在之后承认这是个错误。

下图是大佬的照片：

<img src="https://s2.loli.net/2024/09/29/D7mEzKTJ8dWYyRo.png" alt="image-20240929134346203" style="zoom:67%;" />

除了上述的第三方组件外，大多数的GNU软件是由许多志愿者，在他们的空闲时间，或由公司、教育机构和非营利性组织赞助下撰写。

到 1990 年代初期，操作系统中所需的许多程序（例如库、[编译器](https://en.wikipedia.org/wiki/Compiler)、[文本编辑器](https://en.wikipedia.org/wiki/Text_editor)、[命令行 shell](https://en.wikipedia.org/wiki/Shell_(computing)#Command-line_shells)和[窗口系统](https://en.wikipedia.org/wiki/Windowing_system)）已完成，尽管低级元素如称为[GNU Hurd的](https://en.wikipedia.org/wiki/GNU_Hurd)[设备驱动程序](https://en.wikipedia.org/wiki/Device_driver)、[守护进程](https://en.wikipedia.org/wiki/Daemon_(computer_software))和[内核](https://en.wikipedia.org/wiki/Kernel_(computer_science))都停滞不前且不完整。

1991年，Linux出现。1993年，[FreeBSD](https://zh.m.wikipedia.org/wiki/FreeBSD)发布。FreeBSD就是当年与Unix著作权持有公司达成协议，重写的免费版Unix，所有GNU计划中，运行于用户空间的软件，都可以在Linux或FreeBSD上使用。许多开发者转向于Linux或FreeBSD。其中，Linux成为常见的GNU计划软件运行平台。[理查德·斯托曼](https://zh.m.wikipedia.org/wiki/理查德·斯托曼)主张，Linux操作系统使用了许多GNU计划软件，应正名为[GNU/Linux](https://zh.m.wikipedia.org/wiki/GNU/Linux)，但没有得到Linux社群的一致认同，形成[GNU/Linux命名争议](https://zh.m.wikipedia.org/wiki/GNU/Linux命名爭議)。

[林纳斯·托瓦兹](https://zh.m.wikipedia.org/wiki/林纳斯·托瓦兹)（Linus）开始在[MINIX](https://zh.m.wikipedia.org/wiki/MINIX)上开发[Linux内核](https://zh.m.wikipedia.org/wiki/Linux內核)，为[MINIX](https://zh.m.wikipedia.org/wiki/MINIX)写的软件也可以在[Linux内核](https://zh.m.wikipedia.org/wiki/Linux內核)上使用。[[16\]](https://zh.m.wikipedia.org/zh-cn/Linux#cite_note-16)后来使用GNU软件代替MINIX的软件，因为使用从GNU系统来的源代码可以自由使用，这对Linux的发展有益。使用GNU GPL协议的源代码可以被其他项目所使用，只要这些项目使用同样的协议发布。

对于GNU来说没有构成自己的操作系统是失败的，但是这个失败确实促进了行业进步与发展，[林纳斯·托瓦兹](https://zh.m.wikipedia.org/zh-cn/林納斯·托瓦茲)曾说过如果GNU内核在1991年时可以用，他不会自己去写一个。实际上现在GNU代指的是当年发展下来的一系列软件。

#### GNU的组成

该系统的基本组成包括GNU编译器套装（[GCC](https://zh.m.wikipedia.org/wiki/GCC)）、GNU的C库（[glibc](https://zh.m.wikipedia.org/wiki/Glibc)）、以及GNU核心工具组（[coreutils](https://zh.m.wikipedia.org/wiki/Coreutils)），另外还有GNU调试器（[GDB](https://zh.m.wikipedia.org/wiki/GDB)）、GNU[编程语言](https://zh.m.wikipedia.org/wiki/程式語言)工具程序（[binutils](https://zh.m.wikipedia.org/wiki/Binutils)）、[GNU Cash](https://zh.m.wikipedia.org/w/index.php?title=GNU_Cash&action=edit&redlink=1) shell中[[10\]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-linuxinterface-10) 和GNOME桌面环境。GNU开发人员已经转向GNU应用程序和工具的Linux [移植](https://zh.m.wikipedia.org/wiki/移植_(軟體)) ，现在也广泛应用在其它操作系统中使用，上文我们提到的MinGW实际上叫Minimalist GNU for Windows，也就是向Windows移植的GNU最小套件，是将[GCC](https://zh.wikipedia.org/wiki/GCC)编译器和[GNU Binutils](https://zh.wikipedia.org/wiki/GNU_Binutils)移植到Win32平台下的产物，包括一系列头文件（[Win32API](https://zh.wikipedia.org/wiki/Windows_API)）、[库](https://zh.wikipedia.org/wiki/靜態連結函式庫)和[可执行文件](https://zh.wikipedia.org/wiki/可执行文件)。另有可用于产生32位及64位Windows可执行文件的[**MinGW-w64**](https://zh.wikipedia.org/wiki/Mingw-w64)项目，是从原本MinGW产生的分支。

#### 一点补充

**Binutils**指的是一组二进制程序处理工具，包括：addr2line、ar、objcopy、objdump、as、ld、ldd、readelf、size等。这一组工具是开发和调试不可缺少的工具。

- addr2line：用来将程序地址转换成其所对应的程序源文件及所对应的代码行，也可以得到所对应的函数。该工具将帮助调试器在调试的过程中定位对应的源代码位置。
- as：主要用于汇编。
- ld：主要用于链接。
- ar：主要用于创建静态库。
- ldd：可以用于查看一个可执行程序依赖的共享库。
- objcopy：将一种对象文件翻译成另一种格式，譬如将.bin转换成.elf、或者将.elf转换成.bin等。
- objdump：主要的作用是反汇编。
- readelf：显示有关ELF文件的信息。
- size：列出可执行文件每个部分的尺寸和总尺寸，代码段、数据段、总大小等。

#### MinGW

GCC支持的语言大多在MinGW也受支持，其中涵盖[C](https://zh.wikipedia.org/wiki/C語言)、[C++](https://zh.wikipedia.org/wiki/C%2B%2B)、[Objective-C](https://zh.wikipedia.org/wiki/Objective-C)、[Fortran](https://zh.wikipedia.org/wiki/Fortran)及[Ada](https://zh.wikipedia.org/wiki/Ada)。对于C语言之外的语言，MinGW使用标准的GNU[运行时库](https://zh.wikipedia.org/wiki/运行时库)，如C++使用GNU [libstdc++](https://zh.wikipedia.org/wiki/Libstdc%2B%2B)。

但是MinGW使用Windows中的C**运行时库**。因此用MinGW开发的程序不需要额外的第三方[DLL](https://zh.wikipedia.org/wiki/动态链接库)支持就可以直接在Windows下运行，而且也不一定必须遵从[GPL](https://zh.wikipedia.org/wiki/GPL)许可证。这同时造成了MinGW开发的程序只能使用Win32API和跨平台的第三方库，而缺少POSIX支持，大多数GNU软件无法在不修改源代码的情况下用MinGW编译。

#### 运行时库

谈到运行时库，就不得不在讨论一下C标准，C 标准主要由两部分组成，一部分**描述C的语法**，另一部分**描述C标准库**（描述了一些C标准函数的原型，但是不提供实现）。
C标准库定义了一组标准头文件，每个头文件中包含一些相关的函数、变量、类型声明和宏定义。常见的C标准就是**ANSI C**；为了提高C语言的开发效率，C标准定义了一系列常用的函数，称为**C标准库函数**。应用程序开发者可以包含这些标准函数的头文件，来调用这些C标准函数，来开发应用，这样就可以屏蔽平台的差异；

**C标准库函数**的实现留给了各个系统平台；这个实现就是**C运行时库(C Run Time Libray) ，简称CRT**；C运行库，是和平台相关的，即和操作系统相关的；**C运行库（CRT）**从某种程度上来讲是**C语言的程序和不同操作系统平台之间的抽象层**;接口是统一的标准，实现由各个平台自己实现；Linux和Windows平台下的两个主要C语言运行库分别为glibc(GNU C Library)和MSVCRT（Microsoft Visual C Run-time）。也就是说你如果在Linux下就可以使用glibc，如果使用Windos下的Microsoft Visual C俗称VC IDE，就是用的是**MSVCRT**。

值得注意的是，像线程操作这样的功能并不是标准的C语言运行库的一部分，但是glibc和MSVCRT都包含了线程操作的库函数。所以glibc和MSVCRT事实上是标准C语言运行库的超集，它们各自对C标准库进行了一些扩展。也就是说CRT实际上包含两部分，一部分实现是**基于C标准库**来的，一部分实现是**根据平台自身开发的库**；

某种程度上是**C运行库是C标准库的一个扩展库**，加了很多C标准库所没有的与平台相关的或者不相关的库接口函数。要在一个平台上支持C语言，不仅要实现符合平台的C编译器，还要实现C标准库，这样的实现才算符合C标准。

#### 额外的内容

MinGW使用Windows中的**C运行时库**，也就是默认链接到 Windows 操作系统组件库[MSVCRT](https://en.wikipedia.org/wiki/Microsoft_Windows_library_files#MSVCRT.DLL_and_MSVCPP.DLL)，这句话其实还有更深一层含义，如果你涉及过windos下的UI界面开发就会知道还有一个东西叫**Windows API**，C语言要早于Windows出现，而且C语言实际标准制定的开始时间也要早于Windows（API概念出现的）系统的开发时间。所以Windows系统在开发的时候是完全可以使用C语言的。目前最多的说法是用C和汇编实现的。那么只要用C，就可能用C标准库。

一般情况下，我们说C运行库暗含的意思是哪种平台哪个开发平台的C运行库，CRT的实现是基于Windows API的，而WindowsAPI的开发也是基于C语言的，但不是或者不一定基于CRT（或者C标准库）的。

再深一步，虽然CRT是基于操作系统 API实现的，但并不代表所有的CRT封装了操作系统 API，如一些用户的权限控制，操作系统线程创建等都不属于C运行库，于是对于这些操作我们就不得不直接调用操作系统API或者其他库。

**总结一下，C标准库就是任何平台都可以使用的基本C语言库。而CRT除了将C标准库加入所属范围外，还扩展了与平台相关的接口库，这些接口实现根据不同平台调用不同平台的操作系统API，如下图所示，采用C标准库编写的程序可以应用到windows平台，也可以应用到linux平台；而用CRT另外与平台相关的库函数编写的应用程序不能跨平台运行。**

![202208120030811.jpg](https://s2.loli.net/2022/08/12/o95cNPU2zHdYtsp.jpg)

我们接下来安装MinGW并使用目前全宇宙最好的文字编辑器，编写HelloWorld。最后我们再迁移到更舒服的Clion平台上，并完成环境配置。

## MinGW的环境配置安装

地址如下：https://sourceforge.net/projects/mingw/。

点击下载以后，会打开一个Mingw的安装器的安装，如下图：

![202208120030681.png](https://s2.loli.net/2022/08/12/4ets1C7JVcoZHha.png)

打开exe进行安装，修改安装目录（最好不能有空格），安装完成后进行组件下载：

这一部分就不详细介绍，有兴趣的可以看[稚晖君的博客](https://zhuanlan.zhihu.com/p/145801160)安装，我们在这里使用最新的MinGW64来配置，我个人是两个版本都进行了安装。

因为新版的MinGW64是完全开源的，只提供源码，需要你自己编译，不过我们还可以通过**MSYS2这个工具来下载**新版的MinGW64。

**MSYS2**是一组工具和库，为您提供易于使用的环境，用于构建、安装和运行本机 Windows 软件。

它由一个名为 [mintty](https://mintty.github.io/)的命令行终端、bash、git 和 subversion 等版本控制系统、tar 和 awk 等工具，甚至 autotools 等构建系统组成，所有这些都基于[Cygwin](https://cygwin.com/)的修改版本。尽管其中一些核心部分基于 Cygwin，但 MSYS2 的主要重点是为本地 Windows 软件提供构建环境，并且使用 Cygwin 的部分保持在最低限度。MSYS2 为 GCC、mingw-w64、CPython、CMake、Meson、OpenSSL、FFmpeg、Rust、Ruby 等提供最新的原生构建。

准确的说是集成了[pacman](https://baike.baidu.com/item/pacman)和[Mingw](https://baike.baidu.com/item/Mingw)-w64的[Cygwin](https://baike.baidu.com/item/Cygwin)升级版, 提供了[bash](https://baike.baidu.com/item/bash) shell等[linux](https://baike.baidu.com/item/linux)环境、[版本控制软件](https://baike.baidu.com/item/版本控制软件)（git/hg）和MinGW-w64 工具链。简单的来说模拟Linux运行环境的技术，它的一个优点就在于利用pacman包管理器，我们可以比较轻松的使用Linux包管理器的方式来安装一整套可以在Windows上运行的Linux工具。如果你只是想要在Windows上简单运行一些Linux程序，那么msys2是一个很好的选择。不过我们这里不是想要完全模拟Linux而是我们想通过MSYS2来获取MinGW的工具链。

软件已经放到了开头的网盘里面，安装过程如下：

![image-20240929133950486](https://s2.loli.net/2024/09/29/jk9JsqFoiSuvGE4.png)

一路下一步：

<img src="https://s2.loli.net/2022/08/11/jyZnCUT4oIY8Asg.png" alt="202208111950851.png"  />

![202208120027781.png](https://s2.loli.net/2022/08/12/3YmqUIVcFWanOxM.png)

这时候会打开一个终端，你会很眼熟这不是git那个bash吗？

![202208120027705.png](https://s2.loli.net/2022/08/12/QqMnmxUlWEtDjur.png)

我们首先更新包数据库和基础包。

使用命令：`pacman -Syu`

![202208112009322.png](https://s2.loli.net/2022/08/11/s5uw7zxcvhrEWVR.png)

重新开启后记得在运行一下，完成安装。

![202208112012528.png](https://s2.loli.net/2022/08/11/pCoeSVWmXrKOEJI.png)

更新完成后如下：

![202208112018516.png](https://s2.loli.net/2022/08/11/pMl4Bix6T5keDK2.png)

使用pacman的命令 安装Mingw-W64全部工具链。下图你可以看到他把所有的工具都给你列出来了。

![202208121802836.png](https://s2.loli.net/2022/08/12/B1TJRIZONjECg5a.png)

这里可能需要网络条件好一点，推荐一个魔法，便宜好用：[链接地址](https://jkcloud.net/#/register?code=e4IbbIwA)。

下载完成：

![202208112036892.png](https://s2.loli.net/2022/08/11/zrE6vZmWTXOD8Nf.png)

安装完成以后可以在开始栏里搜到一个神奇的东西：

![202208121802383.png](https://s2.loli.net/2022/08/12/ZQ8Ncx15qJXtlCd.png)

在系统环境变量中添加如下内容：

如果使用的是MinGW32,同样也需要把对应的内容放到环境变量里面。我两个都进行了安装，整体十分方便，基本20mins就搞定了。

![202208120037952.png](https://s2.loli.net/2022/08/12/tdbTAhprQZSlDjK.png)

我们在命令行中测试一下。

![202208112045735.png](https://s2.loli.net/2022/08/11/vguhQ5D8kKNnErq.png)

在网盘里面你也可以找到VScode这里我就不再赘述，直接安装即可。

我们新建一个文件夹C++还有一个Projects文件夹和HelloWorld文件夹,右键通过code打开Helloworld文件夹。

![202208121804786.png](https://s2.loli.net/2022/08/12/7ktKbl3BxPgujfh.png))

注意下图是**错误的**，应该打开对应文件夹，为不是文件，因为VScode需要加入一些调试设置和编译指令：

![202208112058609.png](https://s2.loli.net/2022/08/11/Y7XhBnuQMcEyUwS.png)

然后安装C/C++的插件：

![202208121805542.png](https://s2.loli.net/2022/08/12/hFvePdTBCiKY9WS.png)

当然你也可以不安装直接调用gcc进行编译不过我们为了方便还是安装插件进行调试。插件为我们提供了debug和自动补齐功能：

![202208112101828.png](https://s2.loli.net/2022/08/11/dRL1n2hClckAS5a.png)

我们将鼠标悬停到对象上就会有对象的详细介绍。

![202208112342657.png](https://s2.loli.net/2022/08/11/1h9GtKr38fS7qoV.png)

想要运行的时候，就点击右上角的运行按钮：

![202208112124436.png](https://s2.loli.net/2022/08/11/Pd2RpeEh7GTSjsZ.png)

这时候会让你选择编译器：

![202208112342935.png](https://s2.loli.net/2022/08/11/jYPG69uW4s8KtIw.png)

选择完成以后他会直接编译并且运行：

![202208112124162.png](https://s2.loli.net/2022/08/11/GE7tPRHDkdnswyf.png)

同时**调试控制台**也会输出GDB的信息，你会亲切的看到一个内容他叫GNU，由此你的C++之路就正式开始了。

![202208112125733.png](https://s2.loli.net/2022/08/11/vc6Gfo8FgEhebJ1.png)

如果你仔细了解就会发现，你在选择完编译器以后，VSCode会产生一个文件夹，这是VSCode保存项目信息的文件夹，本着刨根问底的精神我们再来看看这个json文件是干什么的：

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: g++.exe 生成活动文件",
            "command": "C:\\msys64\\mingw64\\bin\\g++.exe",
            "args": [
                "-fdiagnostics-color=always",
                "-g",
                "${file}",
                "-o",
                "${fileDirname}\\${fileBasenameNoExtension}.exe"
            ],
            "options": {
                "cwd": "${fileDirname}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "调试器生成的任务。"
        }
    ],
    "version": "2.0.0"
}
```

tasks指的是当你点下按钮的时候会去执行的任务，  `"command"`设置指定了要运行的程序；在本例中是g++。`args`指定了将被传递给g++的命令行参数。这些参数必须按照编译器所期望的顺序指定。

这个任务告诉g++接收活动文件（ `${file}`），对其进行编译，并在当前目录（  `$ {fileDirname}`）下创建一个可执行文件，其名称与要编译的源码文件名称相同，但扩展名为.exe（${fileBasenameNoExtension}.exe），在我们的例子中产生其实产生了helloworld.exe。

`"label"`值是你将在任务列表中看到的；你可以随心所欲地命名它。

`"detail"`是你将在任务列表中作为任务的描述。强烈建议重命名这个值，以便将它与类似的任务区分开来。

从现在开始，播放按钮将从`tasks.json`中读取信息以确定如何构建和运行你的程序。你可以在`tasks.json`中定义多个编译任务，哪个任务被标记为默认任务，播放按钮就会使用哪个。如果你需要改变默认的编译器，你可以运行Tasks: 配置默认构建任务。或者，你可以修改tasks.json文件，通过替换以下这一段来删除默认任务。

```json
    "group": {
        "kind": "build",
        "isDefault": true
    },
```

把上述任务就可以替换成:

```json
 "group": "build",
```

这样这项任务就不再是默认任务了，执行的时候如果存在多个任务就会让你选择：

![202208112342112.png](https://s2.loli.net/2022/08/11/qU3Gn5vaKYcXLge.png)

如果你想要将这个文件夹下的所有CPP文件进行编译也很简单，我们直接修改args中的参数为：

```json
"args": [
    "-fdiagnostics-color=always",
    "-g",
    "${workspaceFolder}/*.cpp",\\修改这里
    "-o",
    "${fileDirname}\\Jszszzy.exe"
]
```

或者你想要修改对应的输出文件的名称，也很简单，修改对应参数如下：

```json
"args": [
    "-fdiagnostics-color=always",
    "-g",
    "${workspaceFolder}/*.cpp",
    "-o",
    "${fileDirname}\\Jszszzy.exe"\\修改这里
]
```

当然以上这种方式是最原始的，我们还有make、cmake等工具还没有介绍，以后有时间再详细介绍，此篇作为我C++的开篇，搭建好了一个C++的学习使用环境，随着时代变迁比如Clion这种内置CMake的IDE已经出现，更适合大工程，大项目，不过为了学习我们还是使用这种原汁原味的更加深理解。

关于Task的网上有很多介绍，这里不再多说，详情见[链接](https://www.cvmart.net/community/detail/5288)。

我在这里简单演示一下如何同时编译处于两个不同文件夹的源码文件

文件结构如下：

![202208112341690.png](https://s2.loli.net/2022/08/11/GkjNtzD2v18hbXr.png)

task.json配置如下：

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "build1",
            "command": "C:\\msys64\\mingw64\\bin\\g++.exe",
            "args": [
                "-fdiagnostics-color=always",
                "-g",
                "${workspaceFolder}/1/*.cpp",
                "-o",
                "${workspaceFolder}/1\\1.exe"
            ],
            "options": {
                "cwd": "${workspaceFolder}/1"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "detail": "调试器生成的任务1。"
        },
        {
            "type": "cppbuild",
            "label": "build2",
            "command": "C:\\msys64\\mingw64\\bin\\g++.exe",
            "args": [
                "-fdiagnostics-color=always",
                "-g",
                "${workspaceFolder}/2/*.cpp",
                "-o",
                "${workspaceFolder}/2\\2.exe"
            ],
            "options": {
                "cwd": "${workspaceFolder}/2"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "detail": "调试器生成的任务2。"
        },
        {//通过dependsOn来将两个任务合成一个任务
            "label": "build",
            "dependsOn": ["build2", "build1"],
            "problemMatcher": [
                "$gcc"
            ],"group": {
                "kind": "build",
                "isDefault": true//设为默认任务
            },
            "presentation": {  
                "echo": true,  
                "reveal": "always",  
                "focus": true, //这个就设置为true了，运行任务后将焦点聚集到终端，方便进行输入  
                "panel": "new"  
            },
            "command": "2/2.exe",//执行第二个任务
        }

    ],
    "version": "2.0.0"
}

```

使用命令:

![202208112337867.png](https://s2.loli.net/2022/08/11/6wALGRdu1Hz8S9M.png)

最后执行第二个任务：

![202208112338166.png](https://s2.loli.net/2022/08/11/QUFVSRN73X1zusg.png)

参考文章如下：

https://code.visualstudio.com/docs/cpp/config-mingw

https://www.zhihu.com/question/333560253

https://zh.wikipedia.org/wiki/C%2B%2B%E6%A8%99%E6%BA%96%E5%87%BD%E5%BC%8F%E5%BA%AB

https://zh.wikipedia.org/wiki/MinGW

https://www.cnblogs.com/renyuan/p/5031100.html