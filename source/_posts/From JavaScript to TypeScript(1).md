---
title: From JavaScript to TypeScript
categories:
  - javascript
tags:
  - javascript
excerpt: "JavaScript Basic Concepts"
date: 2025/12/30 00:00:00
---

# From JavaScript to TypeScript（1）JavaScript Basic Concepts

<img src="https://s2.loli.net/2026/01/03/cqXdwvMNp81GlDK.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1767455676375" style="zoom:67%;" />



<img src="https://s2.loli.net/2025/12/30/ybuUSFvcs5MhCiE.jpg" alt="nodejs" style="zoom: 67%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom:67%;" />

<!-- toc -->

## JavaScript 的历史

JavaScript 最初被称为 LiveScript，由 Netscape（Netscape Communications Corporation，网景通信公司）公司的布兰登·艾奇（Brendan Eich）在 1995 年开发。在 Netscape 与 Sun（一家互联网公司，全称为“Sun Microsystems”，现已被甲骨文公司收购）合作之后将其更名为了 JavaScript。

之所以将 LiveScript 更名为 JavaScript，是因为 JavaScript 是受 Java 的启发而设计的，因此在语法上它们有很多相似之处，JavaScript 中的许多命名规范也都借鉴自 Java；另一个原因就是为了营销，蹭 Java 的热度。

同一时期，微软和 Nombas（一家名为 Nombas 的公司）也分别开发了 JScript 和 ScriptEase 两种脚本语言，与 JavaScript 形成了三足鼎立之势。它们之间没有统一的标准，不能互用。为了解决这一问题，1997 年，在 ECMA（欧洲计算机制造商协会，European Computer Manufacturers Association）的协调下，Netscape、Sun、微软、Borland（一家软件公司）组成了工作组，并以 JavaScript 为基础制定了 [ECMA-262 标准](https://www.ecma-international.org/publications-and-standards/standards/)（ECMAScript）。

## JavaScript 与 ECMAScript 的关系

ECMAScript（简称“ES”）是根据 ECMA-262 标准实现的通用脚本语言。ECMA-262 标准主要规定了这门语言的语法、类型、语句、关键字、保留字、运算符、对象等部分。ECMAScript 采用按年份发布的版本命名方式，例如 ECMAScript 2024（简称“ES2024”）。

ES6（也称 ES2015，早期代号 ES Harmony）是 JavaScript 发展史上最重要的版本之一。该版本包含了大量关键增强特性，例如类、模块、迭代器、生成器、箭头函数、Promise（期约）、Reflect、Proxy 等。

至于 JavaScript，有时人们会将 JavaScript 与 ECMAScript 看作是相同的，其实不然，JavaScript 中所包含的内容远比 ECMA-262 中规定的多得多，完整的 JavaScript 是由以下三个部分组成：

- 核心（ECMAScript）：提供语言的语法和基本对象；
- 文档对象模型（DOM）：提供处理网页内容的方法和接口；
- 浏览器对象模型（BOM）：提供与浏览器进行交互的方法和接口。

## JavaScript 的特点

JavaScript 具有以下特点：

- #### 解释型脚本语言

  JavaScript 通常不需要像 C、[C++](http://c.biancheng.net/cplus/) 这类语言那样先进行显式编译再运行；在常见宿主环境中，JavaScript 引擎会负责解析并执行代码（通常也会进行 JIT 编译）。

- #### 面向对象

  JavaScript 是一种面向对象语言，使用 JavaScript 不仅可以创建对象，也能操作使用已有的对象。

- #### 弱类型

  JavaScript 是一种弱类型的编程语言，对使用的数据类型没有严格的要求，例如可以将一个变量初始化为任意类型，**也可以随时改变这个变量的类型**。

- #### 动态性

  JavaScript 是一种采用事件驱动的脚本语言，它不需要借助 Web 服务器就可以对用户的输入做出响应，例如我们在访问一个网页时，通过鼠标在网页中进行点击或滚动窗口时，通过 JavaScript 可以直接对这些事件做出响应。

- #### 跨平台

  JavaScript 不依赖特定操作系统，在浏览器中就可以运行。因此一个 JavaScript 脚本在编写完成后可以在任意系统上运行，只需要系统上的浏览器支持 JavaScript 即可。

## 什么是 DOM 和 BOM

DOM 全称是 Document Object Model，也就是文档对象模型。DOM 是 XML（或 HTML）文档在内存中的一种结构化表示和编程接口【API：Application Programming Interface】。

**XML** 是一种**标记语言**，其本身是一个文本文件，用标签来定义数据的结构和含义，它定义了一套通用的语法规则（如必须有根元素、标签成对出现、属性加引号等）。它本身不定义任何具体标签。它是一个“死”的文本字符串。

**DOM** 是一个**编程模型（对象模型）**。当程序（如浏览器、JavaScript、Python 脚本）读取一个 XML 文件时，它会将这个文本文件**解析**成一棵由节点组成的树形结构。这棵树就是 DOM 树。

DOM 通过创建表示文档的树，让开发者可以随心所欲地控制网页内容。更详细见：[DOM 和 BOM 的区别是什么？](https://www.zhihu.com/question/33453164/answer/56549408)

BOM 全称：Browser Object Model，也就是浏览器对象模型。BOM 是一套由浏览器提供的 API，允许 JavaScript 与浏览器窗口（而非网页内容本身）进行交互。它没有像 DOM 那样统一、完整的正式标准（其中部分接口在 HTML 规范中有定义）。

```bash
┌─────────────────────────────────┐
│           浏览器 (Browser)       │ ← 运行环境
├─────────────────────────────────┤
│  BOM (浏览器对象模型)            │ ← JavaScript 访问浏览器的接口
│  ┌──────────────────────────┐  │
│  │  document (DOM 的入口)   │  │ ← 包含 HTML5 文档
│  │  ┌──────────────────┐   │  │
│  │  │  HTML5 文档内容  │   │  │ ← 实际网页内容
│  │  └──────────────────┘   │  │
│  └──────────────────────────┘  │
└─────────────────────────────────┘
```

## Node.js 是什么？

Node.js 不是一门新的编程语言，也不是一个 JavaScript 框架，它是一套 JavaScript 运行环境，用来支持 JavaScript 代码的执行。用编程术语来讲，Node.js 是一个 JavaScript 运行时（Runtime）。所谓运行时，就是程序在运行期间需要依赖的一系列组件或者工具；把这些工具和组件打包在一起提供给程序员，程序员就能运行自己编写的代码了。

对于 JavaScript 来说，它在运行期间需要依赖以下组件：

1. **JavaScript 引擎**

   JavaScript 在运行时依赖 **JavaScript 引擎**，引擎内部结合了解释执行和即时编译（JIT），并非传统意义上的“解释器”。

2. 标准库

   我们在 JavaScript 代码中会调用一些内置函数，这些函数不是我们自己编写的，而是标准库自带的。JavaScript 运行时包含一套 **语言内建标准库（ECMAScript）**，同时还会由宿主环境额外提供 API。

3. **宿主环境提供的 API**
   如浏览器中的 DOM、网络、定时器，或 Node.js 中的文件系统、网络模块。这些能力由底层本地代码实现，但并不属于 JavaScript 语言本身。

## Node.js 和 V8 简介

JavaScript 的一生伴随着浏览器大战。JavaScript 由 Netscape（网景）浏览器发布，但是 Netscape 在和 IE 的竞争中落败，早已不复存在；后来谷歌公司的 Chrome 浏览器异军突起，凭借强悍的性能把 IE 按在地上摩擦。

谷歌公司在 Chrome 浏览器中集成了一种名为“**V8**”的 **JavaScript 引擎**，它能够非常快速地解析和执行 JavaScript 代码。

**V8 引擎使用 C++ 语言编写，可以独立运行，也可以嵌入到任何其它 C++ 程序中。谷歌公司将 V8 引擎甚至整个 Chrome 浏览器都开源了，任何人都可以免费地将 V8 应用到自己的项目中。**

Node.js 是针对 [V8](https://v8.dev/) 引擎构建的。通过与此引擎的最新版本保持同步，确保及时向 Node.js 开发人员提供 [JavaScript ECMA-262 specification](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 中的新功能，以及持续的性能和稳定性改进。



