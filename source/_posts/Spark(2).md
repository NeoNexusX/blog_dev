---
title:  Spark的WSL环境安装与Hadoop环境配置（贰）
categories:
  - Spark
  - Hadoop
  - Scala
tags:
  - Spark
  - Hadoop
  - Scala
  - WSL
excerpt: "Spark的WSL环境安装与Hadoop环境配置"
---

# Spark快速大数据分析——Spark的WSL环境安装与Hadoop环境配置（贰）

<img src="https://s2.loli.net/2024/09/29/AGdij6z9ftuKrh1.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727611823050" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

### 软件环境：

- **Hadoop-3.2**
- **Spark-3.1.3**
- **JDK 8**
- **WSL2** **Ubuntu20.04**
- **Windos10 20H2**

<!-- toc -->

[TOC]



## Spark的WSL环境搭建：

Hadoop还不算折腾人的，当年caffe环境才是最恶心的，装过caffe以后装什么都很有自信。

俗话说的好windos是最好的linux发行版，WSL2是证明这个观点的强有力的证据。开个玩笑。

**先来说如何安装WSL2，注意这里说的是WSL2并不是WSL！**

如果你的电脑是Windos专业版，可以直接跳到WSL2安装部分，如果你的电脑是家庭中文版就先往下看。

### 笔记本Windos如何最快速省事升级专业版？

找到你的**windos激活界面**先查看自己windos版本，如果是家庭版就需要升级来获取高级的系统组件，点击**更改产品密钥**：

<img src="https://s2.loli.net/2024/09/29/FaPKcwdSZJRQM7D.png" alt="1" style="zoom:80%;" />

点击更改密钥：

![2](https://s2.loli.net/2024/09/29/d3OyMjPQxv2uoJL.png)

会让你输入密钥来升级，密钥的获取当然是万能的TB了，直接搜索windos激活码然后先问店家买一个升级码再买一个激活码即可。（注意是两个码！）

![3](https://s2.loli.net/2024/09/29/w42bfKL1o8EFaXi.png)



先输入升级码进行升级，重启后再使用激活码进行激活，一共**7块钱**就可以搞点，虽然有很多升级办法，但是这个还是最省事的，升级过程最好插上电，防止没电。升级过程因为没办法截图就没有截图，过程都是自动的，不需要太多操作这里就不详细赘述了。

升级完成后在系统激活中输入激活码直接激活即可：

![4](https://s2.loli.net/2024/09/29/AVLYUlQJRrGpZ48.png)

激活完成界面：

![5](https://s2.loli.net/2024/09/29/i2uFdL68HORWSZX.png)

这样一来你就升级到了专业版。

### WSL的安装

然后进行WSL的安装：

首先添加系统组件：

![6](https://s2.loli.net/2024/09/29/72G95lHR1Qe4kbO.png)

![7](https://s2.loli.net/2024/09/29/9hdCx1Wy8pTnSMz.png)

确定后等待完成：

![8](https://s2.loli.net/2024/09/29/uDfm85SXjI6cOh4.png)

在windos商店中查找Ubuntu：

![9](https://s2.loli.net/2024/09/29/LvqAomlGQXgPhE7.png)

直接点击下载然后等待安装即可，安装完成以后打开，设置一下用户名和密码：

<img src="https://s2.loli.net/2024/09/29/GVAw8S2c1FY9LmQ.png" alt="10" style="zoom:80%;" />

先关闭界面，因为这里可能安装的是WSL1，不过没关系，我们可以升级一下直接用就行，先检测一下版本：

打开windos cmd或者powershell（以管理员身份打开）：

输入命令：`wsl -l -v`

<img src="https://s2.loli.net/2024/09/29/Jtds9mUN3BFObHu.png" alt="11" style="zoom:80%;" />

如果如图，那么你就安装的是WSL2，可以直接跳到**hadoop安装**，如果你输出的是1请往下看升级步骤：

升级之前先要注意一个问题就是windos的版本：

若要更新到 WSL 2：

- 对于 x64 系统：**版本 1903** 或更高版本，采用**内部版本 18362** 或更高版本。
- 我的是20H2所以没问题，你如果版本不够请先升级windos。

![13](https://s2.loli.net/2024/09/29/DSg9J1oIBzh7jnN.png)

### 升级WSL至WSL2

首先下载Linux内核更新包：[点击下载](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)。

下载完成后直接安装即可：

![12](https://s2.loli.net/2024/09/29/L2TbWX7HBjFkJ8m.png)

如果你没有安装内核更新包直接打开WSL就有可能遇到以下错误：

```SSH
WslRegisterDistribution failed with error: 0x800701bc
Error: 0x800701bc WSL 2 ?????????????????? https://aka.ms/wsl2kernelPress any key to continue.. .
```

![14](https://s2.loli.net/2024/09/29/yDq8HI9sRXYvCZ1.png)

安装内核包以后就没有问题了。

这时候我们再设置一下WSL的默认版本：

打开cmd使用命令：

```ssh
wsl --set-version Ubuntu-20.04 2
```

使用完成后再打开WSL就可以看见：

![15](https://s2.loli.net/2024/09/29/8CRTyZvqBEXHVNP.png)

等一段时间然后按一下回车就可以看见：

可以看到WSL已经更新到了WSL2。

![16](https://s2.loli.net/2024/09/29/BeFguHc83LM74wX.png)

如果你没有这个界面或者更新失败，问题不大，不要慌，卸载然后返回到安装步骤再来一遍即可：

![17](https://s2.loli.net/2024/09/29/xGML4oR71kYh5fH.png)

### WSL2换下载源

首先输入命令备份当前源目录：

```ssh
cd /etc/apt  #进入配置文件所在目录
cp sources.list sources.list.bak  #备份配置文件
```

使用vim编辑源文件：

```ssh
vim sources.list  #编辑配置文件
```

打开后将里面内容全部删除并替换成以下内容：

删除内容方法：

- 按一下ESC键，确保退出编辑模式
- 按两次键盘上面的g键，让光标移动到文本的首行
- 然后按键盘上面的d和G键。其中d键是小写，G键要切换成大写的。

然后切换到编辑模式（输入i）复制进去即可：

```ssh
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

注意换行问题，web可能显示换行实际上每一行都是没有换行的！可以先复制到记事本再copy过去。

复制以后样子如下：

![1-1](https://s2.loli.net/2024/09/29/fVmbMJBXYTw1ytg.png)

退出编辑并保存：esc+wq

使用命令更新源：

```ssh
sudo apt-get update  #更新源  
```

![1-3](https://s2.loli.net/2024/09/29/bcB48ROESynTuH1.png)

更新软件：

```
sudo apt-get upgrade  #更新软件
```

![1-4](https://s2.loli.net/2024/09/29/7SzMs1EZw8Ax2aU.png)

### Hadoop环境配置

首先介绍一下，WSL下一些常用的功能：

在WSL下打开windos的文件浏览器来访问linux下的文件。

输入命令：

`explorer.exe .`

**注意exe后边还有一个空格。**

#### 创建hadoop用户

如果你安装 Ubuntu 的时候不是用的 “hadoop” 用户，那么需要增加一个名为 hadoop 的用户。

```ssh
$ sudo useradd -m hadoop -s /bin/bash
```

这条命令创建了可以登陆的 hadoop 用户，并使用 /bin/bash 作为 shell。（在Ubuntu终端窗口中，复制粘贴的快捷键需要加上 shift，即粘贴是 ctrl+shift+v。）

```ssh
$ sudo passwd hadoop
```

为hadoop用户增加管理员权限：

```ssh
$ sudo adduser hadoop sudo
```

最后输入：

```ssh
$ su hadoop
```

切换到hadoop用户。

### 更新apt

用 hadoop 用户登录后，我们先更新一下 apt，后续我们使用 apt 安装软件，如果没更新可能有一些软件安装不了。 按 ctrl+alt+t 打开终端窗口，执行如下命令：

```ssh
$ sudo apt-get update
```

若出现如下 “Hash校验和不符” 的提示，可通过更改软件源来解决。若没有该问题，则不需要更改。从软件源下载某 些软件的过程中，可能由于网络方面的原因出现没法下载的情况，那么建议更改软件源。

### 安装Vim

```
$ sudo apt-get install vim
```

vim的常用模式有分为命令模式，插入模式，可视模式，正常模式。本教程中，只需要用到正常模式和插入模式。二 者间的切换即可以帮助你完成本指南的学习。

1. 正常模式 正常模式主要用来浏览文本内容。一开始打开vim都是正常模式。在任何模式下按下Esc键就可以返回正常模式 
2. 插入编辑模式 插入编辑模式则用来向文本中添加内容的。在正常模式下，输入i键即可进入插入编辑模式 
3. 退出vim 如果有利用vim修改任何的文本，一定要记得保存。Esc键退回到正常模式中，然后输入:wq即可保存文本并退出 vim。

### 安装SSH、配置SSH无密码登陆

集群、单节点模式都需要用到 SSH 登陆（类似于远程登陆，你可以登录某台 Linux 主机，并且在上面运行命令）， Ubuntu 默认已安装了 SSH client，此外还需要安装 SSH server：

```ssh
$ sudo apt-get install openssh-server
```

安装后，需要重新启动一下：

```ssh
$ sudo service ssh restart
```

输入命令后发现报错：

```ssh
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo service ssh restart
sshd: no hostkeys available -- exiting.
```

使用exit切换到root用户：

```ssh
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ exit
exit
jszszzy@LAPTOP-74GAR8S9:~$
```

检查ssh是否生成密钥，发现没有：

```ssh
jszszzy@LAPTOP-74GAR8S9:~$ cd ~/.ssh/
-bash: cd: /home/jszszzy/.ssh/: No such file or directory
```

再切换到hadoop用户尝试连接一下本地ssh：

```ssh
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo ssh localhost
[sudo] password for hadoop:
ssh: connect to host localhost port 22: Connection refused
```

发现报错：

**ssh: connect to host localhost port 22: Connection refused**

这报错可使用下边的命令解决，或者卸载ssh再重新安装一遍也可能会解决。

使用管理权限开始ssh服务：

```ssh
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo service ssh start
 * Starting OpenBSD Secure Shell server sshd                                             sshd: no hostkeys available -- exiting.
```

**sshd: no hostkeys available -- exiting.**仍报错

没办法手动生成一下hostkey，两种加密方式：

```ssh
sudo ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
```

```ssh
sudo ssh-keygen -t dsa -f /etc/ssh/ssh_host_rsa_key
```

```ssh
sudo ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
Generating public/private dsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /etc/ssh/ssh_host_dsa_key
Your public key has been saved in /etc/ssh/ssh_host_dsa_key.pub
The key fingerprint is:
SHA256:Oizwqo5xcIIVUTAetxHLW8StQ2+oRT/FMAWei/EoKIA root@LAPTOP-74GAR8S9
The key's randomart image is:
+---[DSA 1024]----+
|  *+=o..+=.      |
|.. * =+..oo      |
|E o +oo=o.       |
|o. . o===.       |
|= + ooooS.       |
| = o.o .         |
|. . o +          |
|.o . . .         |
|+o.              |
+----[SHA256]-----+
```

```ssh
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sudo ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /etc/ssh/ssh_host_rsa_key
Your public key has been saved in /etc/ssh/ssh_host_rsa_key.pub
The key fingerprint is:
SHA256:2iivZii9bXWn7GXnkK3oVoEbjuVkfKuVnjocVyABTcM root@LAPTOP-74GAR8S9
The key's randomart image is:
+---[RSA 3072]----+
|      .=+.       |
|        E..      |
|       . o .     |
|        B o .    |
|       BS+ =     |
|      o+* Bo     |
| . ...o+.X=.o    |
|. o.+o  B+o=     |
| ..=o..+=o. .    |
+----[SHA256]-----+
```

如果还没解决，就卸载ssh后安装：

```ssh
sudo apt-get purge openssh-server
```

```ssh
sudo apt-get update
```

```
sudo apt-get install openssh-server
```

最后启动一下：

```
sudo service ssh restart
```

如果还不成功尝试以下命令：

```
sudo ssh-keygen -A
sudo chmod 600 /etc/ssh/ssh_host_rsa_key
sudo chmod 600 /etc/ssh/ssh_host_dsa_key
```

再次打开ssh，发现成功：

```ssh
sudo service ssh restart
 * Restarting OpenBSD Secure Shell server sshd
```

然后查找进程是否存在：

```ssh
hadoop@LAPTOP-74GAR8S9:/home/jszszzy$ sps -e | grep ssh
12765 ?        00:00:00 sshd
```

生成本低公钥添加至免密中：

```
hadoop@LAPTOP-74GAR8S9:~/.ssh$ ssh-keygen -t rsa

Generating public/private rsa key pair.
Enter file in which to save the key (/home/hadoop/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/hadoop/.ssh/id_rsa
Your public key has been saved in /home/hadoop/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:kMkQ2+migmrgDhEKXw4k7pBs8k88Eawq6aqqhFnz2iw hadoop@LAPTOP-74GAR8S9
The key's randomart image is:
+---[RSA 3072]----+
|. ..+.           |
|o+  .* +         |
|*+..+ B          |
|B+.= o .         |
|o+= * . S        |
|B+ * o           |
|@.. o            |
+----[SHA256]-----+
```

添加至免密：

注意当前所在位置：~/.ssh

```
hadoop@LAPTOP-74GAR8S9:~/.ssh$ sudo cat ./id_rsa.pub >> ./authorized_keys
```

测试一下ssh，发现能连接：

```ssh
hadoop@LAPTOP-74GAR8S9:~/windos_pubkey$ ssh localhost

Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.10.16.3-microsoft-standard-WSL2 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Apr 12 22:00:21 CST 2022

  System load:  0.0                Processes:             13
  Usage of /:   0.8% of 250.98GB   Users logged in:       0
  Memory usage: 1%                 IPv4 address for eth0: 172.20.4.176
  Swap usage:   0%


19 updates can be applied immediately.
12 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


Last login: Tue Apr 12 21:38:02 2022 from 127.0.0.1
```

### Java环境配置

推荐使用openjdk。

输入命令直接安装：

```ssh
hadoop@LAPTOP-74GAR8S9:~/windos_pubkey$ sudo apt-get install openjdk-8-jdk
```

安装完成后测试一下：

```ssh
hadoop@LAPTOP-74GAR8S9:~/windos_pubkey$ java -version
openjdk version "1.8.0_312"
OpenJDK Runtime Environment (build 1.8.0_312-8u312-b07-0ubuntu1~20.04-b07)
OpenJDK 64-Bit Server VM (build 25.312-b07, mixed mode)
```

JAVAHOME环境变量设置：

打开环境变量设置：

```ssh
hadoop@LAPTOP-74GAR8S9:~$ vim ~/.bashrc
```

在最下边输入一下内容：

```ssh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

### Hadoop环境配置

下载hadoop 3.2：[hadoop3.2下载](https://archive.apache.org/dist/hadoop/common/hadoop-3.2.2/hadoop-3.2.2.tar.gz)。

使用windos的文件浏览器将下载好的hadoop直接拖拽到root目录对应位置即可：

这里再介绍一种方法打开文件浏览器：

在浏览器上栏输入：

```ssh
\\wsl$
```

效果如下：

![	](https://s2.loli.net/2024/09/29/rYECV8fSRhKolOQ.png)

我拷贝到了这个目录下：

![19](https://s2.loli.net/2024/09/29/mahEoYtvlAxBVcI.png)

然后使用命令解压到`/usr/local`目录下：

```ssh
hadoop@LAPTOP-74GAR8S9:~$ sudo tar -zxf /home/jszszzy/hadoop/hadoop-3.2.2.tar.gz -C /usr/local
```

然后转移到文件夹下，进行一系列操作：

```ssh
$ cd /usr/local/
$ sudo mv ./hadoop-3.1.3/ ./hadoop # 将文件夹名改为hadoop
$ sudo chown -R hadoop ./hadoop # 修改文件权限
```

最后运行一下：

```ssh
$ cd /usr/local/hadoop
$ ./bin/hadoop version
```

### 提示：

相对路径与绝对路径 请务必注意命令中的相对路径与绝对路径，本文后续出现的 ./bin/... ， ./etc/... 等包含 ./ 的路径，均 为相对路径，以 /usr/local/hadoop 为当前目录。例如在 /usr/local/hadoop 目录中执行 ./bin/hadoop version 等同于执行 /usr/local/hadoop/bin/hadoop version 。可以将相对路径改成绝对路径来执行，但 如果你是在主文件夹 ~ 中执行 ./bin/hadoop version ，执行的会是 /home/hadoop/bin/hadoop version ， 就不是我们所想要的了。

下一篇将会介绍Hadoop单机配置、Hadoop伪分布式配置。
