---
title:  Spark快速大数据分析——Spark基础（肆）
categories:
  - Spark
  - Hadoop
  - Scala
tags:
  - Spark
  - Hadoop
  - Scala
excerpt: "Spark基础原理。"
---

# Spark快速大数据分析——Spark基础（肆）

<img src="https://s2.loli.net/2024/09/29/NbsHdGrJ1IkZFVY.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727612362790" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

## 软件环境

**强烈提示：
请按照对应配套版本来进行环境配置！**

- **Hadoop-3.2**
- **IDEA 2021.3.2**
- **Spark-3.1.3 搭配  scala版本：2.12  构建方式：IDEA导入本地JAR，Spark库**
- **Spark-3.2.1 搭配  scala版本 ：2.13.8 构建方式 ：SBT自动导入依赖**
- **JDK 8**

<!-- toc -->

[TOC]



## Spark的分布式执行

为了方便理解概念，我将完整的交互关系图画了出来，可以一边看文章配合着这张图来理解：

![1_37e](https://s2.loli.net/2024/09/29/FLwSZTncIHJm3ez.jpg)

### Spark驱动器

作为Spark应用中负责初始化的SparkSession的部分，Spark驱动器扮演着多个角色：

- **与集群管理器打交道**，**向集群管理器申请Spark执行器（JVM）所需要的资源**；
- **将Spark操作转换为DAG运算，并负责调度，还要将计算分成任务分发到Spark执行器上**。

### SparkSession

在Spark2.0之中，SparkSession是所有Spark操作和数据的统一入口。它不仅封装了Spark程序之前的各种入口，还让Spark变得更加简单。

2.x和1.x版本的区别在哪里呢？

在1.x版本你需要创建多个上下文对象（分别对数据进行处理，sql查询等），这会让代码显得很繁琐。但在Spark2.x中你只需要为每个JVM创建一个Sparksession即可，就可以通过Sparksession调用全部对象。

在Spark2.x版本中虽然Sparksession已经包含了其中操作的各种对象例如使用SparkContext和SQLContext产生的对象，你也可以直接旧版的1.x的代码来使用这些类和对象，也就是说2.x兼容1.x版本的代码。

通过Sparksession入口，可以创建JVM运行时的参数、定义DataFrame或Dataset、从数据源读取数据、访问数据库的元数据，并发起SQL查询。SparkSession为所有的Spark功能提供了统一的入口。

### 集群管理器

集群管理器负责管理和分配各个节点的资源，以用来Spark应用的执行。

目前Spark支持4种集群管理器：

- Spark自带的独立集群管理器
- Apache Hadoop YARN
- Apache Mesos
- Kubernetes

### Spark执行器

Spark执行器在各个节点上进行工作，**执行器与驱动器程序通信**，**并负责在工作节点上执行任务**。**在大多数部署模式，每个节点上的只有一个执行器。**

### 部署模式

目前spark支持的部署模式如下：

<img src="https://s2.loli.net/2024/09/29/tdMcbAOW8VigraU.png" alt="9" style="zoom:67%;" />

常用的就是本地模式和独立模式。

### 分布式数据与分区

实际上物理数据是以分区的形式分布在**HDFS**或者云储存上的，数据分区遍布整个物理集群，而Spark将每个分区在逻辑上抽象为内存中的一个Dataframe（和结构化数据结构不相同），根据本地要求在分配任务的时候根据要求的数据分区与各Spark执行器上在网络上的远近，将数据分配到最近的Spark执行器上，将数据分区的分布式结构可以让Spark执行器只处理靠近自己的数据，从而最小化网络带宽的使用率。也就是说，执行器的每个核心都能分到自己要处理数据的分区。

如下图展示了Spark的分区数据结构：

![10](https://s2.loli.net/2024/09/29/Ahf81IDBSlu2gHn.png)

## 深入理解Spark的基础概念

### 应用

使用Spark的API构建基于Spark的用户程序。

一般由一个驱动器和集群内多个执行器组成。

### SparkSession

SparkSession对象提供与下层Spark功能交互的入口。它允许用户用Spark API编写Spark程序。在交互式Spark shell种，Spark驱动器已经初始化了一个SparkSession对象，但在Spark应用中，你需要自行创建SparkSession对象。

所有Spark应用的核心都是Spark驱动器程序，他要负责创建Spark Session对象当使用Spark shell的时候驱动器是shell的一部分，SparkSeesion对象已经自动创建好了，在启动Spark-shell的时候可以看见：

![1](https://s2.loli.net/2024/09/29/PZF4zJnpf1RxH8s.png)

在前边windos下Spark的启动都是以本地模式启动的 Spark shell，因此所有操作都会在本地的单个JVM中执行。如果要在集群中启动Spark shell来并行分析数据，其实和本地模式差不多，使用spark-shell --help来展示如何连接：

![2](https://s2.loli.net/2024/09/29/hfXPEbrjAyNdags.png)

下图展示了Spark分布式集群管理器是如何通过驱动器实现各节点连接的

![3](https://s2.loli.net/2024/09/29/ZUqNKnly37dmktb.png)

### 作业

在Spark-shell进行交互式会话中，**驱动器会将Spark应用转为一个或者多个Spark作业**，如图所示：

![4](https://s2.loli.net/2024/09/29/D86SsfQrUl7OFME.png)

驱动器会自动将每个作业转化为一个DAG（**有向无环图**），这个DAG本质上就是Spark运行的执行计划，每一个节点都有可能是一个或者多个Spark执行阶段，具体什么是执行阶段接着往下看.

### 执行阶段

Spark执行阶段可以作为任务计划的一个节点，也就是DAG图中的一个节点，Spark会根据操作能否并行执行或者必须串行执行来创建执行阶段。**不是所有的Spark操作只对应一个阶段，某些操作可能是多个阶段的。**

**执行阶段之间可能会触发Spark执行器间的数据传输。**

下图展示了一个作业如何被分解成一个DAG内多个执行阶段的。

![5](https://s2.loli.net/2024/09/29/dTackD8lmRW9NMO.png)

### 任务

执行阶段是由Spark任务组成的，这些任务由Spark的各个执行器一起执行。每个任务对应一个**处理器核心**（cpu 物理上的核心或者超线程），并处理一个**数据分区**，从而使Spark的任务执行高度并行化，更重要的是不需要用户自己具体考虑如何具体并行化。

下图展示了Spark执行阶段创建的多个任务：

![6](https://s2.loli.net/2024/09/29/swEJA18OlvkgWjb.png)



### Spark的操作

Spark对分布式数据的操作可以分为两种类型:**转化操作和行动操作**。

#### 转化操作与惰性求值

顾名思义，转化操作就是将Spark DataFrame转化为新的DataFrame，而不改变原有的数据，这赋予了DataFrame不可变的属性，并不改变原有的数据，这样就赋予了Dataframe不可变的属性。换句话说，像select()[选择数据]或者filter()[过滤数据]这种操作并不会修改原有的数据，这些操作会将数据返回成一个新的DataFrame。

所有转化操作都是惰性求值的，也就是说，具体结果不会立即计算出来，Spark只是将具体的转化关系作为血缘(lineage）记录下来，记录下来的血缘允许Spark在后续生成执行计划时重新安排要做的转化操作，比如合并多个转化操作，或者优化为不同的执行阶段来提高执行效率。惰性求值是Spark的策略，目的是直到调用行动操作或“碰”到数据（从硬盘上读取或向硬盘写入)时才真正执行行动操作会触发所记录下来的所有转化操作的实际求值。

#### 行动操作

行动操作会触发记录下来的所有转化操作的实际求值。也就是说转化操作会被行动操作所触发。

![7](https://s2.loli.net/2024/09/29/nqUu3i69ycXILNG.png)

如上图，在调用A操作前，所有转化操作T都只是被记录下来了。每个操作T都会生成一个新的DataFrame

**惰性求值允许Spark通过综合分析整个转化操作链来优化查询。**

**血缘（lineage）和数据不可变性则让Spark拥有容错性。只需要通过Spark记录下来的血缘就可以重新构建出DataFrame，这样Spark便能够在失败的时候依然有机会恢复！**

常用的转化与行动操作：

<img src="https://s2.loli.net/2024/09/29/ag1k8B2hNLuv3rG.png" alt="8" style="zoom: 67%;" />

### Spark的查询计划

行动操作和转化操作生成了Spark的查询计划，以后会对此进行介绍。调用行动操作之前Spark并不会执行查询计划。只有行动操作会触发查询计划的执行，此时查询计划中包含的的所有记录下来的转化操作才会真正执行。

### 窄转化与宽转化

如前文所述，转化操作是Spark特有的惰性求值操作。惰性求值方案的最大优势是，Spark可以分析整个计算查询，然后针对计算查询过程进行分析优化，到后边我们会举例Spark是如何优化的。这里的优化包括将一些操作连起来放在一个执行阶段中进行管道化执行，或者根据是否需要集群节点进行数据交换或者混洗，将操作分为多个阶段来执行。

根据依赖关系属于窄依赖还是宽依赖可以将转化操作分为两类。如果输出的单个数据分区是由单个输入分区计算得来的，那么这样的转化操作就成为窄转化。比如filter()和contains()就属于窄转化，因为这些操作在每个数据分区是独立的，生成的输出数据分区时不需要跨分区进行操作。

然而，groupby()和orderby（）会产生宽转化操作。对于统计每个分区存在单词“spark”的出现次数，需要从每个执行器上将来自于各个分区的数据进行混洗。具体是如何实现的留到后面再说。

![18](https://s2.loli.net/2024/09/29/zOsunEPx7Jpy2wh.png)

以上就是Spark的一些基础概念，下节将介绍Spark代码具体的应用。























