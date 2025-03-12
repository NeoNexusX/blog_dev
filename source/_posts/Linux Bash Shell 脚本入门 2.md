​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      

---
title: Linux Bash Shell 脚本入门（2）——GNU
categories:
  - Linux
tags:
  - Linux
excerpt: "GNU 介绍 GNOME桌面环境介绍 Shell介绍"
date: "2025/03/12 18:37:00"
---

# Linux Bash Shell 脚本入门（2）——GNU



[TOC]

<!-- toc -->

<img src="https://s2.loli.net/2025/03/12/FvsRHCyO4QK9Tlb.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1741775577228" style="zoom:67%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1695021721029" style="zoom: 50%;" />

可点击内容，大部分来自维基百科，可以点击了解详情。

## GNU与Linux

### GNU

GNU操作系统起源于[GNU计划](https://zh.m.wikipedia.org/wiki/GNU計劃)，由[理查德·斯托曼](https://zh.m.wikipedia.org/wiki/理查·斯托曼)在[麻省理工学院](https://zh.m.wikipedia.org/wiki/麻省理工學院)人工智能实验室发起，希望发展出一套完整的开放源代码操作系统来取代Unix，计划中的操作系统，名为GNU。

![246px-Heckert_GNU_white.svg](https://s2.loli.net/2022/08/12/jQLouxXq39ZigGR.png)

GNU的商标是一个[牛羚](https://zh.m.wikipedia.org/wiki/牛羚)头。原先版本是Etienne Suvasa所设计，而今比较流行的粗体版本则是Aurelio Heckert所设计。

**GNU**是一个[自由](https://zh.m.wikipedia.org/wiki/自由軟體)的[操作系统](https://zh.m.wikipedia.org/wiki/作業系統)，其内容软件完全以[GPL](https://zh.m.wikipedia.org/wiki/GPL)方式发布（什么是GPL下文会介绍）。这套操作系统是[GNU计划](https://zh.m.wikipedia.org/wiki/GNU計劃)的主要目标，名称来自GNU's Not Unix!的[递归缩写](https://zh.m.wikipedia.org/wiki/遞迴縮寫)，因为GNU的设计类似[Unix](https://zh.m.wikipedia.org/wiki/Unix)，但它不包含具著作权的Unix代码。

读到这里你可能会难以理解，我从来没听说过GNU操作系统，像是Linux，Unix这种都是很有名的，但GNU到底是什么？

1983年9月27日，[理查德·斯托曼](https://zh.m.wikipedia.org/wiki/理查·斯托曼)在 net.unix-wizards 和 net.usoft[新闻组](https://zh.m.wikipedia.org/wiki/新聞群組)中公布这项GNU计划。斯托曼的目标是成立一个完全自由的操作系统，他希望电脑用户是能够“自由使用”的。

为什么要在这里强调自由使用呢？因为在20世纪60年代和70年代 - 大多数人都能自由学习软件的源代码，自由地与他人分享的软件，可自由修改软件，自由发布软件修改后的版本。也就是说早些年软件几乎不存在著作权，每个人都可以自由地使用和修改。

1984年1月5日，理查德正式开始开发软件，为了防止这些软件将来可能被主张所有权，影响到Free Software的发展，斯托曼辞去了在实验室的工作。

理查德·斯托曼使用[不兼容分时系统](https://zh.m.wikipedia.org/w/index.php?title=不兼容分時系統&action=edit&redlink=1)（英语：[Incompatible Timesharing System](https://en.wikipedia.org/wiki/Incompatible_Timesharing_System)） (ITS)[[6]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-intervention-6)（一种早期的操作系统，使用[汇编语言](https://zh.m.wikipedia.org/wiki/汇编语言)撰写，因其所运行的被称为 [PDP-10](https://zh.m.wikipedia.org/wiki/PDP-10)的电脑系统架构停止发展而变得过时）的经验，决定了需要设计一种可移植系统。由此，会议决定，将开发新的系统，并使用 [C](https://zh.m.wikipedia.org/wiki/C语言) 和[Lisp](https://zh.m.wikipedia.org/wiki/Lisp)作为系统编程语言。[[8]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-8) GNU将与UNIX兼容。[[9]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-9) 当时，UNIX已经是一个流行的专有操作系统。而Unix的设计是模块化的，所以它可以被逐步分块的实现。

Unix在学术机构和大型企业中得到了广泛的应用，当时的UNIX拥有者[AT&T](https://zh.m.wikipedia.org/wiki/AT%26T)公司以低廉甚至免费的许可将Unix源码授权给学术机构做研究或教学之用，许多机构在此源码基础上加以扩展和改进，形成了所谓的“Unix变种”，这些变种反过来也促进了Unix的发展。

后来[AT&T](https://zh.m.wikipedia.org/wiki/AT%26T)意识到了Unix的商业价值，不再将Unix源码授权给学术机构，并对之前的Unix及其变种声明了著作权权利。BSD在Unix的历史发展中具有相当大的影响力，被很多商业厂家采用，成为很多商用Unix的基础。其不断增大的影响力终于引起了AT&T的关注，于是开始了一场持久的著作权官司，至此人们意识到我们需要一个完全免费的操作系统。

1984年，开始发展编辑器[Emacs](https://zh.m.wikipedia.org/wiki/Emacs)等软件。1985年，发表[GNU宣言](https://zh.m.wikipedia.org/wiki/GNU宣言)。1989年，发表[GNU通用公共许可协议](https://zh.m.wikipedia.org/zh-cn/GNU_General_Public_License)。这个公共开源许可协议，你如果开源过项目一定见过，它叫GPL协议，GPL是[自由软件](https://zh.m.wikipedia.org/wiki/自由軟件)和[开源软件](https://zh.m.wikipedia.org/wiki/开源软件)的最流行许可证[[18]](https://zh.m.wikipedia.org/zh-cn/GNU通用公共许可证#cite_note-18)。到2004年4月，GPL已占[Freshmeat](https://zh.m.wikipedia.org/w/index.php?title=Freshmeat&action=edit&redlink=1&variant=zh-cn)（英语：[Freshmeat](https://en.wikipedia.org/wiki/Freshmeat)）上所列的自由软件的约75%，[SourceForge](https://zh.m.wikipedia.org/wiki/SourceForge)的约68%。类似的，2001年一项关于[Red Hat Linux](https://zh.m.wikipedia.org/wiki/Red_Hat_Linux) 7.1的调查显示一般的代码都以GPL发布。著名的GPL自由软件包括[Emacs](https://zh.m.wikipedia.org/wiki/Emacs)，[Linux](https://zh.m.wikipedia.org/wiki/Linux)核心（并非所有[Linux发行版](https://zh.m.wikipedia.org/wiki/Linux發行版)的核心都是开源的）和[GCC](https://zh.m.wikipedia.org/wiki/GCC)。

我们回到GNU的操作系统来，因为许多必要的软件需要从零开始写起来，但是有很多已经存在的第三方组件如 [TeX](https://zh.m.wikipedia.org/wiki/TeX)、[X Window System](https://zh.m.wikipedia.org/wiki/X_Window_System)[[4]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-internethist-4)和[Mach](https://zh.m.wikipedia.org/wiki/Mach)微内核等等都可以进行利用，其中GNU最关键系统内核，是在[GNU Mach](https://zh.m.wikipedia.org/zh-cn/GNU_Mach)[微内核](https://zh.m.wikipedia.org/wiki/微內核)基础上进行开发，**理查德·马修·斯托曼**认为可以借此加速操作系统的开发，但因为一直不确定[卡内基梅隆大学](https://zh.m.wikipedia.org/wiki/卡内基梅隆大学)何时要将核心源代码发布，造成计划延宕三年。他在之后承认这是个错误。

下图是大佬的照片：

<img src="https://s3.bmp.ovh/imgs/2022/08/12/b0dde8c65ef4b8b8.jpg" alt="Richard_Stallman_at_LibrePlanet_2019" style="zoom:67%;" />

除了上述的第三方组件外，大多数的GNU软件是由许多志愿者，在他们的空闲时间，或由公司、教育机构和非营利性组织赞助下撰写。

到 1990 年代初期，操作系统中所需的许多程序（例如库、[编译器](https://en.wikipedia.org/wiki/Compiler)、[文本编辑器](https://en.wikipedia.org/wiki/Text_editor)、[命令行 shell](https://en.wikipedia.org/wiki/Shell_(computing)#Command-line_shells)和[窗口系统](https://en.wikipedia.org/wiki/Windowing_system)）已完成，尽管一些底层元素如称为[GNU Hurd的](https://en.wikipedia.org/wiki/GNU_Hurd)[设备驱动程序](https://en.wikipedia.org/wiki/Device_driver)、[守护进程](https://en.wikipedia.org/wiki/Daemon_(computer_software))和[内核](https://en.wikipedia.org/wiki/Kernel_(computer_science))都停滞不前且不完整。

1991年，Linux出现。1993年，[FreeBSD](https://zh.m.wikipedia.org/wiki/FreeBSD)发布。FreeBSD就是当年与Unix著作权持有公司达成协议重写的免费版Unix，所有GNU计划中，运行于用户空间的软件，都可以在Linux或FreeBSD上使用。许多开发者转向于Linux或FreeBSD。其中，Linux成为常见的GNU计划软件运行平台。[理查德·斯托曼](https://zh.m.wikipedia.org/wiki/理查德·斯托曼)主张，Linux操作系统使用了许多GNU计划软件，应正名为[GNU/Linux](https://zh.m.wikipedia.org/wiki/GNU/Linux)，但没有得到Linux社群的一致认同，形成[GNU/Linux命名争议](https://zh.m.wikipedia.org/wiki/GNU/Linux命名爭議)。

[林纳斯·托瓦兹](https://zh.m.wikipedia.org/wiki/林纳斯·托瓦兹)（Linus）开始在[MINIX](https://zh.m.wikipedia.org/wiki/MINIX)上开发[Linux内核](https://zh.m.wikipedia.org/wiki/Linux內核)，为[MINIX](https://zh.m.wikipedia.org/wiki/MINIX)写的软件也可以在[Linux内核](https://zh.m.wikipedia.org/wiki/Linux內核)上使用。1991年Linus[[16\]](https://zh.m.wikipedia.org/zh-cn/Linux#cite_note-16)后来使用GNU软件代替MINIX的软件，因为使用从GNU系统来的源代码可以自由使用，这对Linux的发展有益。同时在GPL的规定下，使用GNU GPL协议的源代码可以被其他项目所使用，只要这些项目使用同样的协议发布源码。

对于GNU来说没有构成自己的操作系统是失败的，但是这个失败确实促进了行业进步与发展，[林纳斯·托瓦兹](https://zh.m.wikipedia.org/zh-cn/林納斯·托瓦茲)曾说过如果GNU内核在1991年时可以用，他不会自己去写一个。实际上现在GNU代指的是当年发展下来的一系列软件。

#### GNU的组成

该系统的基本组成包括

- GNU编译器套装（[GCC](https://zh.m.wikipedia.org/wiki/GCC)）
- GNU的C库（[glibc](https://zh.m.wikipedia.org/wiki/Glibc)）
- GNU核心工具组（[coreutils](https://zh.m.wikipedia.org/wiki/Coreutils)）
- GNU调试器（[GDB](https://zh.m.wikipedia.org/wiki/GDB)）
- GNU[编程语言](https://zh.m.wikipedia.org/wiki/程式語言)工具程序（[binutils](https://zh.m.wikipedia.org/wiki/Binutils)）
- [GNU Bash](https://zh.m.wikipedia.org/w/index.php?title=GNU_Bash) shell[[10]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-linuxinterface-10) 
- GNOME桌面环境

GNU开发人员已经转向GNU应用程序和工具的Linux 移植 ，现在也广泛应用在其它操作系统中使用，上文我们提到的MinGW实际上叫Minimalist GNU for Windows，也就是向Windows移植的GNU最小套件，是将[GCC](https://zh.wikipedia.org/wiki/GCC)编译器和[GNU Binutils](https://zh.wikipedia.org/wiki/GNU_Binutils)移植到Win32平台下的产物，包括一系列头文件（[Win32API](https://zh.wikipedia.org/wiki/Windows_API)）、[库](https://zh.wikipedia.org/wiki/靜態連結函式庫)和[可执行文件](https://zh.wikipedia.org/wiki/可执行文件)。另有可用于产生32位及64位Windows可执行文件的[**MinGW-w64**](https://zh.wikipedia.org/wiki/Mingw-w64)项目，是从原本MinGW产生的分支。

### Shell

shell是种特殊的交互工具，为用户提供了启动程序，管理文件系统中的文件及运行在Linux系统中的进程的途径。shell的核心就是命令行提示符，负责shell的交互部分，允许用户输入文本命令，然后解释命令并在内核中执行。

常见的命令被用来复制、移动、重命名文件等，你用鼠标实现的操作，在shell中都可以实现，除此之外，shell也允许在命令行提示符中输入程序的名称，他会将程序名称传递给内核以启动程序。

同时也允许，将多个shell命令放入文件中作为程序执行。这些文件成为shell脚本。凡是能在命令行中执行的命令都可以放入shell脚本中作为一组命令执行。

linux中所有发行版的默认几乎都是bash shell，bash shell 是[GNU Bash](https://zh.m.wikipedia.org/w/index.php?title=GNU_Bash) shell[[10]](https://zh.m.wikipedia.org/zh-cn/GNU#cite_note-linuxinterface-10) ，目的是为了替代老版本Unix系统中的Unix shell，Bash shell名字的由来非常有意思，其名字来源于一个谐音梗 Bourne Again Shell （念作 born again）的缩写，Linux 中当然不仅仅包括GNU的 Bash Shell 也包含其他的shell比如：

- ash：轻量级别的shell,适合运行在内存受限的环境中，比如嵌入式等。
- korn：一种与Born Shell兼容的shell,但支持一些高级特性，浮点运算等。
- tcsh：一种包含了一些C语言特性引入到shell脚本的shell。
- zsh：一种结合了bash、korn、tcsh的特性的shell。

关于shell的内容会在以后的内容中详细介绍，这里就不再赘述。

### GNOME桌面环境

随着Linux系统的发展，最初的操作系统文本界面到图形化的发展，操作系统不仅仅服务于计算机开发人员，逐渐走向大众化，用户不再满足于陈旧的文本命令行交互，图形化的操作界面的日渐强烈，当然完成工作的方式不只有一种，Linux一直以来都以此为闻名。图形化桌面更是如此。Linux有多种图形化桌面可供选择。接下来会介绍一种属于GNU项目的桌面环境。

#### X Window 软件

有两个基本要素决定了你的视频环境，显卡和显示器，显卡如何让显示器显示就需要软件来实现，Linux中负责这一部分的就是X Window，X Window是图形现实的核心部分，控制Linux应用如何在显示器上呈现漂亮的窗口和图形。X Window System，通常简称为X11或X，是一个基于位图的显示系统，用于在位图显示器上提供图形用户界面（GUI）。

X Window System的关键特性包括：

- **网络透明性**：X Window系统设计之初就考虑到了网络支持，允许应用程序的图形界面可以在网络上的任何地方显示，实现了客户端与服务器之间的通信。这意味着，一个运行在网络中某台计算机上的应用程序可以将其图形界面显示在连接到同一网络的另一台计算机上。
- **客户端-服务器架构**：在X Window系统中，"服务器"是运行在用户机器上的程序，它管理屏幕、键盘、鼠标等输入输出设备。"客户端"是指任何请求服务器服务的程序，比如图形应用程序。这里的“服务器”和“客户端”的概念与常规意义上的网络服务和使用者相反。
- **设备和网络独立性**：X Window系统与具体的硬件设备和网络协议无关，可以在各种不同的硬件和网络环境中运行。
- **灵活性和扩展性**：X Window提供了基本的框架和协议，开发者可以在其之上构建各种窗口管理器和桌面环境，如GNOME、KDE等，这使得用户界面可以高度定制化。

尽管X Window本身不是驱动程序，但它依赖于底层的硬件驱动程序来进行图形渲染和设备输入输出操作。例如，X服务器需要与显卡的驱动程序交互，以控制屏幕上的图形显示。因此，为了使X Window系统正常工作，相应的硬件设备（如显卡、键盘、鼠标等）需要有正确安装的驱动程序。

Linux中能够实现  X window的软件不止有一种，Linux中最常见用的有：

- wayland
-  X.org

X.org基于最初的Unix X Windos System版本11 ，后来大家把他简称为X11,属于常用的两种的比较老的版本，新版的linux正在向wayland迁移，虽然这样说，但是目前Ubuntu22.04默认的还是X11:

<img src="https://s2.loli.net/2024/02/11/hdk7HJRvCAWS5Zz.png" alt="image-20240211210131407" style="zoom: 67%;" />

在首次安装Ubuntu的时X11会检测显卡和显示器，然后创建一个含有必要信息的X Window配置文件。在安装过程中，安装程序会检测一次显示器，以确定所支持的视频模式。有时这会造成显示器黑屏几秒。由于显卡和显示器的种类繁多，因此这个过程可能需要花费一点时间。

核心的X Window 软件能够生常图形化现实环境，但仅此而已。尽管这已足以运行单独的应用程序，但在日常的计算机使用中并不是特别有用，因为毕竟没有提供桌面环境，用户无法知道该如何启动程序，操作文件，为此需要在此基础上建立一个桌面环境。

注意一下，下图驱动部分是Nouveau 驱动，是X11的下层驱动：

![image-20240211211054589](https://s2.loli.net/2024/02/11/HRAUjEODPgsiutK.png)

至于想搞明白每一个环节的具体工作可以参考：

[GNOME，Xorg，X Window，X Server，Wayland是什么关系]: https://www.zhihu.com/question/503270852
[揭开Wayland的面纱（一）：X Window的前生今世]: https://cloud.tencent.com/developer/article/1442279

### GNOME

<img src="https://s2.loli.net/2024/02/11/IkGQJ24R6WjDlVH.png" alt="image-20240211215028684" style="zoom:67%;" />

Gnome桌面工作在Xorg-Server的上层，提供给用户相应的桌面环境，其全称是（GNU Network object model enviroment，GNU网络对象模型环境）是另一个流行的Linux桌面环境，GNOME于1990年首次发布，现已成为许多Linux发行版默认的桌面环境。

随着发展GNOME已经度过了很多个版本，可以通过以下命令来查看当前的GNOME版本：

```shell
neo@NeoNeuxs:~/Desktop$ gnome-shell --version
GNOME Shell 42.9
```

GNOME桌面环境又被称为GNOME-Shell，为什么会有shell这个单词呢？

"Shell"（壳）在这里的含义与其在计算机科学中的一般含义相似，指的是一个提供用户界面的软件层，用户可以通过这个界面与系统的更深层次的功能进行交互，在操作系统中，"shell"通常指的是提供与用户交互的界面，这可以是命令行界面（CLI）或图形用户界面（GUI）。GNOME Shell 是后者的一个例子，它提供了一个图形界面供用户启动程序、管理窗口、切换任务等。

一个新版的Gnome-shell如下：

![Snipaste_2024-02-11_22-25-07](https://s2.loli.net/2024/02/11/7lv8XCqNnodGhca.png)

参考：

[wiki]: https://en.wikipedia.org/wiki/GNU
