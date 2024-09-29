---
title:  Spark快速大数据分析——Spark的Hadoop配置（叁）
categories:
  - Spark
  - Hadoop
  - Scala
tags:
  - Spark
  - Hadoop
  - Scala
excerpt: "Spark的Hadoop配置，Hadoop的单机模式无需额外配置，直接运行即可，适合调试；伪分布式模式需修改配置文件，并运行HDFS读取文件。运行MapReduce时，确保输出目录不存在，必要时提前删除避免报错。"
---

# Spark快速大数据分析——Spark的Hadoop配置（叁）

<img src="https://s2.loli.net/2024/09/29/JGuU5PY7BSrQzAX.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727612092601" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

## 软件环境：

- **Hadoop-3.2**
- **Spark-3.1.3**
- **JDK 8**
- **WSL2** **Ubuntu20.04**
- **Windos10 20H2**

<!-- toc -->

[TOC]



## Hadoop单机配置(非分布式)

Hadoop 默认模式为非分布式模式（本地模式），无需进行其他配置即可运行。非分布式即单 Java 进程，方便进行 调试。 

现在我们可以执行例子来测试一下Hadoop 的运行是否正常。

Hadoop 附带了丰富的例子（运行 ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar 可以看到所有例子），包括 wordcount、 terasort、join、grep 等。

在此我们选择运行grep例子，我们将input文件夹的所有文件作为输入，筛选当中符合正则表达式 dfs[a-z.]+ 的单 词并统计出现的次数，最后输出结果到 output 文件夹中。

首先切换到hadoop用户下：

```ssh
jszszzy@LAPTOP-74GAR8S9:~$ su hadoop
```

切换对应文件夹下：

```ssh
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ cd /usr/local/hadoop
```

创建一个input文件夹，把配置文件拷贝过来，将配置文件作为输入文件：

```
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ mkdir ./input
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ cp ./etc/hadoop/*.xml ./input
```

最后调用运行，注意不要换行！（显示的可能有换行），注意版本和你本机安装的版本相对应：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./bin/hadoop jar./share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.2.jar grep input output 'dfs[a-z.]+'
```

如果在这个地方产生报错：

```shell
Error: Could not find or load main class jar..share.hadoop.mapreduce.hadoop
```

请删除hadoop重新解压一下，删除命令如下，解压命令参考第三篇：

```shell
cd /usr/local
```

```shell
sudo rm -r hadoop
```

等待运行完成后查看结果：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ cat ./output/*
1       dfsadmin
```

完成后结果如下：

![1](https://s2.loli.net/2024/09/29/xeak7WG54hVpm2o.png)

显示符合正则的单词 dfsadmin 出现了1次。

### 注意！

注意，Hadoop 默认不会覆盖结果文件，因此再次运行上面实例会提示出错，需要先将 ./output 删除。

删除命令：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ rm -r ./output
```

## Hadoop伪分布式配置

Hadoop 可以在单节点上以伪分布式的方式运行，Hadoop 进程以分离的 Java 进程来运行，节点既作为 NameNode 也作为 DataNode，同时，读取的是 HDFS 中的文件。

 Hadoop 的配置文件位于 /usr/local/hadoop/etc/hadoop/ 中，伪分布式需要修改2个配置文件 core-site.xml 和 hdfssite.xml 。Hadoop的配置文件是 xml 格式，每个配置以声明 property 的 name 和 value 的方式来实现。

修改配置文件 core-site.xml： vim ./etc/hadoop/core-site.xml ，将当中的

```ssh
<configuration>
</configuration>
```

修改为下面配置：

```ssh
<configuration>
 	<property>
 		<name>hadoop.tmp.dir</name>
 		<value>file:/usr/local/hadoop/tmp</value>
		 <description>Abase for other temporary directories.</description>
 	</property>
 	<property>
 		<name>fs.defaultFS</name>
		 <value>hdfs://localhost:9000</value>
	 </property>
 </configuration>
```

同样的，修改配置文件 hdfs-site.xml：

```ssh
<configuration>
	<property>
		<name>dfs.replication</name>
		<value>1</value>
	</property>
	<property>
		<name>dfs.namenode.name.dir</name>
		<value>file:/usr/local/hadoop/tmp/dfs/name</value>
	</property>
	<property>
		<name>dfs.datanode.data.dir</name>
		<value>file:/usr/local/hadoop/tmp/dfs/data</value>
	</property>
</configuration>
```

### Hadoop配置文件说明 

Hadoop 的运行方式是由配置文件决定的（运行 Hadoop 时会读取配置文件），因此如果需要从伪分布式模 式切换回非分布式模式，需要删除 core-site.xml 中的配置项。 

此外，伪分布式虽然只需要配置 fs.defaultFS 和 dfs.replication 就可以运行（官方教程如此），不过若没有 配置 hadoop.tmp.dir 参数，则默认使用的临时目录为 /tmp/hadoo-hadoop，而这个目录在重启时有可能被系 统清理掉，导致必须重新执行 format 才行。所以我们进行了设置，同时也指定 dfs.namenode.name.dir 和 dfs.datanode.data.dir，否则在接下来的步骤中可能会出错。



我们继续回到配置：

配置完成后，执行 NameNode 的格式化，注意这里路径是什么:

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./bin/hdfs namenode -format
```

成功的话，会看到 “successfully formatted” 的提示，具体返回信息类似如下：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./bin/hdfs namenode -format
WARNING: /usr/local/hadoop/logs does not exist. Creating.
2022-04-14 13:20:01,596 INFO namenode.NameNode: STARTUP_MSG:
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = LAPTOP-74GAR8S9.localdomain/127.0.1.1
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 3.2.2
STARTUP_MSG:   classpath = /usr/local/hadoop/etc/hadoop:/usr/local/hadoop/share/hadoop/common/lib/netty-3.10.6.Final.
```

![2](https://s2.loli.net/2024/09/29/y4HaB7jmtohMGkJ.png)

### 注意！

如果在这一步报错：

**Error: JAVA_HOME is not set and could not be found.**

则说明之前设置 JAVA_HOME 环境变量那边就没设置好，请按教程先设置好 JAVA_HOME 变量，否则后面的过程都是进行不下去 。

如果已经按照前面教程在.bashrc文件中设置了JAVA_HOME。还是出现 Error: JAVA_HOME is not set and could not be found. 的错误。

那么，请到hadoop的安装目录修改配置文件/usr/local/hadoop/etc/hadoop/hadoop-env.sh”，在里面找到“export JAVA_HOME=${JAVA_HOME}”这行，然后，把它修改成JAVA安装路径的具体地址，比 如，“export JAVA_HOME=/usr/lib/jvm/default-java”，然后，再次启动Hadoop。

设置后仍报错：

![3](https://s2.loli.net/2024/09/29/7lAQHfOWvVSZ28b.png)

打开环境配置文件：

添加：`export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` 

![4](https://s2.loli.net/2024/09/29/cnD3CpoeQkMh8NT.png)

接着开启NameNode和DataNode守护进程:

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ cd /usr/local/hadoop
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./sbin/start-dfs.sh
```

输入命令以后开始运行：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ cd /usr/local/hadoop
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./sbin/start-dfs.sh
```

如果有报错如下：

```shell
hadoop@LAPTOP-HO0AHQKI:/usr/local/hadoop$ sudo ./sbin/start-dfs.sh
WARNING: HADOOP_SECURE_DN_USER has been replaced by HDFS_DATANODE_SECURE_USER. Using value of HADOOP_SECURE_DN_USER.
Starting namenodes on [localhost]
localhost: root@localhost: Permission denied (publickey).
Starting datanodes
localhost: root@localhost: Permission denied (publickey).
Starting secondary namenodes [LAPTOP-HO0AHQKI]
LAPTOP-HO0AHQKI: root@laptop-ho0ahqki: Permission denied (publickey).
```

请使用：

```ssh
exit
```

**退出ssh！**

### 注意！

若出现如下SSH提示，输入yes即可：

![5](https://s2.loli.net/2024/09/29/n71om5iAyrFUMkN.png)

启动时可能会出现如下 WARN 提示：WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform… using builtin-java classes where applicable WARN 提示可以忽略，并不会影响正常使用。

启动 Hadoop 时提示 Could not resolve hostname 如果启动 Hadoop 时遇到输出非常多“ssh: Could not resolve hostname xxx”的异常情况，如下图所示：

![6](https://s2.loli.net/2024/09/29/SmkjwXlIM2osNAY.png)

这个并不是 ssh 的问题，可通过设置 Hadoop 环境变量来解决。首先按键盘的 ctrl + c 中断启动，然后在 ~/.bashrc 中，增加如下两行内容（设置过程与 JAVA_HOME 变量一样，其中 HADOOP_HOME 为 Hadoop 的安装目录）：

```
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
```

保存后，务必执行 source ~/.bashrc 使变量设置生效，然后再次执行 ./sbin/start-dfs.sh 启动 Hadoop。

启动完成后，可以通过命令 jps 来判断是否成功启动，若成功启动则会列出如下进程: “NameNode”、”DataNode” 和 “SecondaryNameNode”（如果 SecondaryNameNode 没有启动，请运行 sbin/stop-dfs.sh 关闭进程，然后再次尝 试启动尝试）。如果没有 NameNode 或 DataNode ，那就是配置不成功，请仔细检查之前步骤，或通过查看启动日志排查原因。

使用后：

![7](https://s2.loli.net/2024/09/29/gJ8zF6boCySwmda.png)

### Hadoop无法正常启动的解决方法

-  一般可以查看启动日志来排查原因，注意几点： 启动时会提示形如 “DBLab-XMU: starting namenode, logging to /usr/local/hadoop/logs/hadoop-hadoopnamenode-DBLab-XMU.out”，其中 DBLab-XMU 对应你的机器名，但其实启动日志信息是记录在 /usr/local/hadoop/logs/hadoop-hadoop-namenode-DBLab-XMU.log 中，所以应该查看这个后缀为 .log 的 文件；
-  每一次的启动日志都是追加在日志文件之后，所以得拉到最后面看，对比下记录的时间就知道了。
-  一般出错的提示在最后面，通常是写着 Fatal、Error、Warning 或者 Java Exception 的地方。 
- 可以在网上搜索一下出错信息，看能否找到一些相关的解决方法

此外，若是 DataNode 没有启动，可尝试如下的方法（注意这会删除 HDFS 中原有的所有数据，如果原有的 数据很重要请不要这样做）：

```ssh
$ # 针对 DataNode 没法启动的解决方法
$ cd /usr/local/hadoop
$ ./sbin/stop-dfs.sh # 关闭
$ rm -r ./tmp # 删除 tmp 文件，注意这会删除 HDFS 中原有的所有数据
$ ./bin/hdfs namenode -format # 重新格式化 NameNode
$ ./sbin/start-dfs.sh # 重启
```

## 运行Hadoop伪分布式实例

上面的单机模式，grep 例子读取的是本地数据，伪分布式读取的则是 HDFS 上的数据。要使用 HDFS，首先需要在 HDFS 中创建用户目录：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./bin/hdfs dfs -mkdir -p /user/hadoop
```

接着将 ./etc/hadoop 中的 xml 文件作为输入文件复制到分布式文件系统中，即将 /usr/local/hadoop/etc/hadoop 复制 到分布式文件系统中的 /user/hadoop/input 中。我们使用的是 hadoop 用户，并且已创建相应的用户目录 /user/hadoop ，因此在命令中就可以使用相对路径如 input，其对应的绝对路径就是 /user/hadoop/input:

```
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./bin/hdfs dfs -mkdir  -p /user/hadoop/input
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./bin/hdfs dfs -put ./etc/hadoop/*.xml input
```

可以使用ls查看一下文件信息：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./bin/hdfs dfs -ls input
Found 9 items
-rw-r--r--   1 hadoop supergroup       9213 2022-04-14 13:37 input/capacity-scheduler.xml
-rw-r--r--   1 hadoop supergroup       1033 2022-04-14 13:37 input/core-site.xml
-rw-r--r--   1 hadoop supergroup      11392 2022-04-14 13:37 input/hadoop-policy.xml
-rw-r--r--   1 hadoop supergroup       1079 2022-04-14 13:37 input/hdfs-site.xml
-rw-r--r--   1 hadoop supergroup        620 2022-04-14 13:37 input/httpfs-site.xml
-rw-r--r--   1 hadoop supergroup       3518 2022-04-14 13:37 input/kms-acls.xml
-rw-r--r--   1 hadoop supergroup        682 2022-04-14 13:37 input/kms-site.xml
-rw-r--r--   1 hadoop supergroup        758 2022-04-14 13:37 input/mapred-site.xml
-rw-r--r--   1 hadoop supergroup        690 2022-04-14 13:37 input/yarn-site.xml
```

伪分布式运行 MapReduce 作业的方式跟单机模式相同，区别在于伪分布式读取的是HDFS中的文件（可以将单机步 骤中创建的本地 input 文件夹，输出结果 output 文件夹都删掉来验证这一点）。

注意：命令没有换行：

```ssh
 hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.2.jar grep input output 'dfs[a-z.]+'
```

![8](https://s2.loli.net/2024/09/29/IsaTWyZhPClrBKt.png)

查看运行结果的命令（查看的是位于 HDFS 中的输出结果）：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./bin/hdfs dfs -cat output/*
1       dfsadmin
1       dfs.replication
1       dfs.namenode.name.dir
1       dfs.datanode.data.dir
```

Hadoop 运行程序时，输出目录不能存在，否则会提示错误 **“org.apache.hadoop.mapred.FileAlreadyExistsException: Output directory hdfs://localhost:9000/user/hadoop/output already exists”** 

因此若要再次执行，需要执行如下命令删除 output 文件夹:

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$ ./bin/hdfs dfs -rm -r output
Deleted output
```

### 注意！

运行程序时，输出目录不能存在 运行 Hadoop 程序时，为了防止覆盖结果，程序指定的输出目录（如 output）不能存在，否则会提示错误， 因此运行前需要先删除输出目录。在实际开发应用程序时，可考虑在程序中加上如下代码，能在每次运行时 自动删除输出目录，避免繁琐的命令行操作：

```java
Configuration conf = new Configuration();
Job job = new Job(conf);
/* 删除输出目录 */
Path outputPath = new Path(args[1]);
outputPath.getFileSystem(conf).delete(outputPath, true);
```

若要关闭 Hadoop，则运行：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hadoop$  ./sbin/stop-dfs.sh
Stopping namenodes on [localhost]
Stopping datanodes
Stopping secondary namenodes [LAPTOP-74GAR8S9]
```

