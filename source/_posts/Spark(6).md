---
title: Spark快速大数据分析——Hive的部署与使用（陆）
categories:
  - Spark
  - Hadoop
  - Scala
  - Hive
tags:
  - Spark
  - Hadoop
  - Scala
  - Hive
excerpt: "Hive的部署与使用。"
date: 2024/10/22 00:00:00
---

# Spark快速大数据分析——Hive的部署与使用（陆）



<img src="https://s2.loli.net/2024/09/29/9cbeUOHyknhTrLY.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727612918890" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

<!-- toc -->

[TOC]

## 软件环境：

**强烈提示：
请按照对应配套版本来进行环境配置！**

- **Hadoop-3.2**
- **IDEA 2021.3.2**
- **Spark-3.1.3 搭配  scala版本：2.12  构建方式：IDEA导入本地JAR，Spark库**
- **JDK 8**
- **Hive 3.1.3**
- **MYSQL 8.0.2** 
- **WSL 2 Ubuntu20.04**

**备注：在安装Hive3.1.2之前，请首先安装Hadoop3.2。**

## 一、安装Hive

**温馨提示：**

```ssh
jszszzy@LAPTOP-74GAR8S9:~$
```

此此前缀代表的是当前用户和目录，并不是命令的一部分。

首先需要下载Hive安装包文件， [Hive官网下载地址](http://www.apache.org/dyn/closer.cgi/hive/)。

开启文件浏览器，将下载的压缩包放入进去，可以先放到root用户目录下：

```ssh
jszszzy@LAPTOP-74GAR8S9:~$ explorer.exe .
```

![3](https://s2.loli.net/2024/09/29/1ZQRP9H72qwaKzj.png)

最好先下载好hive的配置文件一起放入进去：

地址，下载压缩包解压即可。

注意是：**hive-site.xml**

![4](https://s2.loli.net/2024/09/29/K1bVlXgxoNkeqth.png)

将对应文件解压过去：

此处我事先创建了一个hive文件夹，用来存放内容，可自行通过文件浏览器创建。

```ssh
jszszzy@LAPTOP-74GAR8S9:~$ cd hive
jszszzy@LAPTOP-74GAR8S9:~/hive$ sudo tar -zxvf ./apache-hive-3.1.3-bin.tar.gz -C /usr/local
```

随后前往对应文件夹来进行修改：

```ssh
jszszzy@LAPTOP-74GAR8S9:~/hive$ cd /usr/local/
jszszzy@LAPTOP-74GAR8S9:/usr/local$ sudo mv apache-hive-3.1.3-bin hive
```

修改完名字后再修改权限：

```ssh
jszszzy@LAPTOP-74GAR8S9:/usr/local$ sudo chown -R jszszzy:jszszzy hive
```

添加环境变量方便运行：

```ssh
jszszzy@LAPTOP-74GAR8S9:/usr/local$ vim ~ /.bashrc
```

在最后添加的内容如下：

```
export HIVE_HOME=/usr/local/hive
export PATH=$PATH:$HIVE_HOME/bin
export HADOOP_HOME=/usr/local/hadoop
```

生效修改：

```ssh
jszszzy@LAPTOP-74GAR8S9:/usr/local$ source ~/.bashrc
```

别忘了hadoop用户相应的权限。

先切换到hadoop用户：

```
su hadoop
```

转移到对应文件夹：

```
hadoop@LAPTOP-74GAR8S9:/usr/local$ cd /usr/local/
hadoop@LAPTOP-74GAR8S9:/usr/local$ sudo chown -R hadoop:hadoop hive
```

记得添加环境变量这里就不再赘述：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local$  vim ~/.bashrc
hadoop@LAPTOP-74GAR8S9:/usr/local$ source ~/.bashrc
```

添加的内容相同。

修改hive的配置文件：

```
hadoop@LAPTOP-74GAR8S9:/usr/local$ cd /usr/local/hive/conf
```

修改hive的配置文件的名字：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hive/conf$ mv hive-default.xml.template hive-default.xml
```

打开配置文件：

```ssh
hadoop@LAPTOP-74GAR8S9:/usr/local/hive/conf$ vim hive-site.xml
```

在hive-site.xml中添加如下配置信息：

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
  <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true</value>
    <description>JDBC connect string for a JDBC metastore</description>
  </property>
  
  <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
    <description>Driver class name for a JDBC metastore</description>
  </property>
  
  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>hive</value>
    <description>username to use against metastore database</description>
  </property>
  
  <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>hive</value>
    <description>password to use against metastore database</description>
  </property>
</configuration>
```

你可能注意到复制过程可能会导致过程混乱，所以推荐你下载我写好的，直接通过文件浏览器复制过去就行了，当然对于这个目录是直接无法访问的，所以推荐先拷贝在root用户下，然后复制过去。

拷贝到root用户下使用命令，然后拖拽过去：

```
jszszzy@LAPTOP-74GAR8S9:~$ explorer.exe .
```

修改配置文件为新的配置文件：

```
hadoop@LAPTOP-74GAR8S9:/usr/local/hive/conf$ rm hive-site.xml
hadoop@LAPTOP-74GAR8S9:/usr/local/hive/conf$ cp /home/jszszzy/hive-site.xml /usr/local/hive/conf
```

## 二、安装并配置Mysql

这一部分是最折腾的部分，每个人的情况都可能不太一样，所以版本的内容要尽量和我的一模一样再来开始。

版本的信息内容我已放到最开头部分，请仔细查看。

目前发文时期最新的APT安装的MYSQL是可以使用的，但是过程有些小麻烦，详见如下：

网上的安装人云亦云，什么在Mysql的辅助版本下安装等方法层出不穷，我基本都做了尝试，你如果使用的是WSL2最好和我的方法保持一致。

避免浪费时间。

**如下这种方法，完全不行，始终无法检测到mysql的状态，不推荐使用：**

![14](https://s2.loli.net/2024/09/29/5IB3gMEm7TxGOoj.png)

我们直接使用APT来安装：

```ssh
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo apt-get update
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo apt-get install mysql-server -y
```

安装过程稍微有些长，如果你没有换源请看我前几篇是如何更换下载源的。

下载过程：

```
eading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  libcgi-fast-perl libcgi-pm-perl libencode-locale-perl libevent-core-2.1-7 libevent-pthreads-2.1-7 libfcgi-perl
  libhtml-parser-perl libhtml-tagset-perl libhtml-template-perl libhttp-date-perl libhttp-message-perl libio-html-perl
  liblwp-mediatypes-perl libmecab2 libtimedate-perl liburi-perl mecab-ipadic mecab-ipadic-utf8 mecab-utils
  mysql-client-8.0 mysql-client-core-8.0 mysql-common mysql-server-8.0 mysql-server-core-8.0
Suggested packages:
  libdata-dump-perl libipc-sharedcache-perl libwww-perl mailx tinyca
The following NEW packages will be installed:
  libcgi-fast-perl libcgi-pm-perl libencode-locale-perl libevent-core-2.1-7 libevent-pthreads-2.1-7 libfcgi-perl
...............................................
省略部分信息
.................................................
关键地方需要看一眼：
done!
update-alternatives: using /var/lib/mecab/dic/ipadic-utf8 to provide /var/lib/mecab/dic/debian (mecab-dictionary) in auto mode
Setting up libhtml-parser-perl (3.72-5) ...
Setting up libhttp-message-perl (6.22-1) ...
Setting up mysql-server-8.0 (8.0.29-0ubuntu0.20.04.3) ...
invoke-rc.d: could not determine current runlevel
 * Stopping MySQL database server mysqld                                                                         [ OK ]
update-alternatives: using /etc/mysql/mysql.cnf to provide /etc/mysql/my.cnf (my.cnf) in auto mode
Renaming removed key_buffer and myisam-recover options (if present)
mysqld will log errors to /var/log/mysql/error.log
mysqld is running as pid 782
Created symlink /etc/systemd/system/multi-user.target.wants/mysql.service → /lib/systemd/system/mysql.service.
invoke-rc.d: could not determine current runlevel
Setting up libcgi-pm-perl (4.46-1) ...
Setting up libhtml-template-perl (2.97-1) ...
Setting up mysql-server (8.0.29-0ubuntu0.20.04.3) ...
Setting up libcgi-fast-perl (1:2.15-1) ...
Processing triggers for systemd (245.4-4ubuntu3.16) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for libc-bin (2.31-0ubuntu9.7) ...
/sbin/ldconfig.real: /usr/lib/wsl/lib/libcuda.so.1 is not a symbolic link
```

在最后安装完成的时候会提示：

```ssh
 * Stopping MySQL database server mysqld                                           [ OK ]
```

不知道为什么自动关闭了，不过问题不大我们手动打开：

```ssh
sudo service mysql restart
```

这里推荐使用sudo，不知道为什么这里输入hadoop的密码后还是会报错导致无法开启。

![20](https://s2.loli.net/2024/09/29/C7gZ3dAB456WyPz.png)

使用sudo：

![N2YC41{K(SWNKQ)DPL%ZB$F](https://s2.loli.net/2024/09/29/dVzHSY3bE2O4Fic.png)

打开后我们再来检测一下状态，发现仍是STOP？？？？这就是一个很离谱的bug，说明他启动没有成功。

中间还有可能出现一个小问题：

问题如下：

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql/mysql.conf.d$ sudo service mysql start
 * Starting MySQL database server mysqld                                                 su: warning: cannot change directory to /nonexistent: No such file or directory [ OK ]
```

解决办法是：

```ssh
# Ubuntu
sudo service mysql stop
sudo usermod -d /var/lib/mysql/ mysql
sudo service mysql start
```

不过这里我们先放放无法启动的问题，你如果检测的状态如下：

```ssh
hadoop@LAPTOP-74GAR8S9:/var/lib/mysql$ sudo service mysql status
mysql: unrecognized service
```

这说明没有安装成功推荐安装办法如下：

记得先卸载原来安装的！

卸载SQL：

```
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo apt purge mysql-*
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo rm -rf /etc/mysql/ /var/lib/mysql
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo apt autoremove
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo apt autoclean
```

如果你的检测状态如下，恭喜你一遍就安装成功了，很幸运。请直接跳到Hive的mysql配置部分。

```
* /usr/bin/mysqladmin  Ver 8.0.29-0ubuntu0.20.04.3 for Linux on x86_64 ((Ubuntu))
Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version          8.0.29-0ubuntu0.20.04.3
Protocol version        10
Connection              Localhost via UNIX socket
UNIX socket             /var/run/mysqld/mysqld.sock
Uptime:                 8 sec

Threads: 2  Questions: 8  Slow queries: 0  Opens: 436  Flush tables: 3  Open tables: 27  Queries per second avg: 1.000
```

如果你的状态是上图那个，请你继续往下看：

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql/mysql.conf.d$ service mysql status
 * MySQL is stopped.
```

我们再来看一下默认的3306端口有东西吗？

```
hadoop@LAPTOP-74GAR8S9:/etc/mysql/mysql.conf.d$ netstat -an|grep 3306
```

如果你没有输出，就继续往下看。

我们先尝试登陆一下MySQL看看具体情况如何：

你到这里会意识到一个问题，我的MYSQL密码是什么？初始化的时候并没有设置，所以这里应该是有一个随机密码的，我们先找到随机密码：

使用如下命令：

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql/mysql.conf.d$ sudo cat /etc/mysql/debian.cnf
# Automatically generated for Debian scripts. DO NOT TOUCH!
[client]
host     = localhost
user     = debian-sys-maint
password = dC6kQukIgXCUqqto
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
host     = localhost
user     = debian-sys-maint
password = dC6kQukIgXCUqqto
socket   = /var/run/mysqld/mysqld.sock
```

其中包含了一个默认的用户名（**debian-sys-maint**）和随机密码（**dC6kQukIgXCUqqto**）。

我们就用这个登录：

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql/mysql.conf.d$ mysql -udebian-sys-maint -p
Enter password:
```

注意输入密码的时候是看不见所以要分清大小写一个一个登录。

如果你在这里报错：

```ssh
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (13)
```

那就有点麻烦了，注意你报错的如果是这个，那可能是你的密码不正确，再多尝试几次：

```
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
```

登陆成功你可以跳转到MYSQL配置HIVE的部分，如果还是报错，请往下看。

MYSQL有两种登陆方式，一个是通过ssh一个是通过TCP链接，我们尝试通过TCP链接一下：

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql/mysql.conf.d$ mysql -u debian-sys-maint -h 127.0.0.1 -p
Enter password:
```

果然通过TCP就可以了，说明我们的**socket**出了问题，一般是再MYSQL服务启动的时候会制动生成**mysqld.sock**，这里说是不行我就觉得很离谱。

不过没关系我也找到了解决办法：

首先可以尝试如下操作：

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql$ cd /var/run/mysqld/
bash: cd: /var/run/mysqld/: Permission denied
```

竟然被拒绝，先给对应的文件加上权限，可访问可运行：

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql$ sudo chmod 777 /var/run/mysqld/
```

加上权限后再次检测状态，应该可以检测到了，如果还不能！

不慌！我们还有办法：

在安装的时候有个重要信息：

```ssh
update-alternatives: using /etc/mysql/mysql.cnf to provide /etc/mysql/my.cnf (my.cnf) in auto mode
```

意思是：在自动模式下使用**/etc/mysql/my.cnf (my.cnf)**的配置文件去替代**/etc/mysql/mysql.cnf** 的配置文件，这就意味着我们可以使用他来替代这个文件，不知道为什么这个配置文件会有这样的BUG，我在SO上找到了解决办法：

注意！请先使用cp备份一下。

```
hadoop@LAPTOP-74GAR8S9:/etc/mysql$ sudo cp my.cnf my.cnf.buckup
```

删除对应配置文件，实际上应该是一个软连接到了mysql.cnf上，所以问题不大。

```ssh
hadoop@LAPTOP-74GAR8S9:/var/lib/mysql$ sudo rm -rf /etc/mysql/my.cnf
```

重启一下:

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql$ sudo service mysql restart
```

再次检测状态：

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql$ sudo service mysql status
```

问题解决：

```ssh
adoop@LAPTOP-74GAR8S9:/etc/mysql$ sudo service mysql status
 * /usr/bin/mysqladmin  Ver 8.0.29-0ubuntu0.20.04.3 for Linux on x86_64 ((Ubuntu))
Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version          8.0.29-0ubuntu0.20.04.3
Protocol version        10
Connection              Localhost via UNIX socket
UNIX socket             /var/run/mysqld/mysqld.sock
Uptime:                 8 sec

Threads: 2  Questions: 8  Slow queries: 0  Opens: 436  Flush tables: 3  Open tables: 27  Queries per second avg: 1.000
```

还有个小bug：

```
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo service mysql restart
 * Stopping MySQL database server mysqld                                          [ OK ]
 * /etc/init.d/mysql: WARNING: /etc/mysql/my.cnf cannot be read. See README.Debian.gz
 * Starting MySQL database server mysqld										[ OK ]
```

我们删除后会导致有警告，问题不大：

我们将备份还原即可：

注意所在路径。

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql$ sudo mv my.cnf.buckup my.cnf
```

登录MYSQL配置HIVE所需环境，你刚才是用来TCP登陆了，不过这里就是使用socket登录，密码还是前文随机的密码。

```
mysql -u debian-sys-maint -p
```

登陆结果如下：

```ssh
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 8.0.29-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

修改初始密码：

选择数据库，`use mysql`

```
mysql> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
```

先修改密码：`alter user 'root'@'localhost' identified by '123456';`

```
mysql> alter user 'root'@'localhost' identified by '123456';
Query OK, 0 rows affected (0.00 sec)

mysql> select user,plugin from user;
+------------------+-----------------------+
| user             | plugin                |
+------------------+-----------------------+
| debian-sys-maint | caching_sha2_password |
| mysql.infoschema | caching_sha2_password |
| mysql.session    | caching_sha2_password |
| mysql.sys        | caching_sha2_password |
| root             | auth_socket           |
+------------------+-----------------------+
5 rows in set (0.00 sec)
```

修改其密码格式
`update user set plugin='mysql_native_password' where user='root';`

查询其用户`select user,plugin from user;`

```ssh
mysql> update user set plugin='mysql_native_password' where user='root';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select user,plugin from user;
+------------------+-----------------------+
| user             | plugin                |
+------------------+-----------------------+
| debian-sys-maint | caching_sha2_password |
| mysql.infoschema | caching_sha2_password |
| mysql.session    | caching_sha2_password |
| mysql.sys        | caching_sha2_password |
| root             | mysql_native_password |
+------------------+-----------------------+
5 rows in set (0.00 sec)
```

保存权限修改：

```ssh
mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)
```

最后退出：

```ssh
mysql> quit
Bye
```

尝试登陆：

```ssh
hadoop@LAPTOP-74GAR8S9:/etc/mysql/mysql.conf.d$ mysql -u root -p
Enter password:
```

登陆成功。

![18](https://s2.loli.net/2024/09/29/PBhkb6jfCXrNGxU.png)

## Hive的mysql配置部分

剩下的请参考厦门大学林子雨老师的博客：[大数据实验室](http://dblab.xmu.edu.cn/blog/2440-2/)。直接到配置完成MYSQL,这部分有时间再写！

