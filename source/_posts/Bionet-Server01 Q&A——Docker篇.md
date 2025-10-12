---
title: BionetServer-No1. Q&A-Docker
categories:
  - Bionet
tags:
  - Bionet
  - ops
  - Q&A
date: "2024/09/21 20:46:25"
---

# BionetServer-No1. Q&A-Docker

<img src="https://s2.loli.net/2024/09/29/qWjluktz8hxAV4E.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727547630579" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

Version:1.0


Date: 2025.10.10

Authors：NeoNexus

<!-- toc -->

[TOC]



## Q&A：

### 问题零 通过容器写入的文件无法运行？

容器的用户都是root身份，而UI登陆的是以用户的身份来登陆的，普通用户当然没有权限来操作root的内容，解决办法：

将文件修改成主机所有者，在容器的终端中使用命令

```bash
sudo chown UID 文件/目录路径
```

如果要修改某个文件夹的全部文件，可以使用：

```bash
sudo chown -R UID 文件/目录路径
```

UID信息在UI界面使用命令获取，比如：

![image-20240607235021091](https://s2.loli.net/2024/06/07/dhEWOpuyINHF6SY.png)

打个比方，修改我在主机上：/Datasets/bionet/Dataset/A/ 文件夹的可以使用：

```bash
sudo chown -R 1001 /Datasets/bionet/Dataset/A/
```

这样这个文件夹下所有文件都可以正常使用。

R语言已经实现了UID的同步，所以没有这个问题。

这里放一个在Jupyter中修改权限的的例子：

```bash
# ls -alh
total 8.0K
drwxr-xr-x 2 root root 4.0K Apr 18  2022 .
drwxr-xr-x 1 root root 4.0K Jun  7 15:51 ..
# cd /tf 首先要到对应的文件夹下来操作文件
# ls -alh
total 6.9M
drwxrwxrwx  1 root root 4.0K Jun  7 15:51 .
drwxr-xr-x  1 root root 4.0K Jun  7 15:51 ..
drwxrwxrwx 11 1001 1002   14 Jun  5 05:03 Datasets
drwxrwxrwx  6 root root 6.8M May 26 06:23 NCZone
drwxrwxr--  6 1001 1001 4.0K Jun  7 15:30 Share_Space
drwxrwxrwx  1 root root 4.0K Sep  2  2023 tensorflow-tutorials
# cd Share_Space
# ls -alh
total 28K
drwxrwxr-- 6 1001 1001 4.0K Jun  7 15:30 .
drwxrwxrwx 1 root root 4.0K Jun  7 15:51 ..
drwx------ 4 root root 4.0K Jun  7 15:30 .Trash-0
drwxr-xr-x 2 root root 4.0K Jun  7 15:30 .ipynb_checkpoints
drwxrwxr-- 6 root root 4.0K May  4 11:07 PTM
drwxrwxr-- 2 1000 1000 4.0K Jun  7 09:13 TEST
-rwxrwxr-- 1 root root 2.2K Apr 23 15:11 test.py
# pwd
/tf/Share_Space
# sudo chown 1001 TEST
# ls -alh
total 28K
drwxrwxr-- 6 1001 1001 4.0K Jun  7 15:30 .
drwxrwxrwx 1 root root 4.0K Jun  7 15:51 ..
drwx------ 4 root root 4.0K Jun  7 15:30 .Trash-0
drwxr-xr-x 2 root root 4.0K Jun  7 15:30 .ipynb_checkpoints
drwxrwxr-- 6 root root 4.0K May  4 11:07 PTM
drwxrwxr-- 2 1001 1000 4.0K Jun  7 09:13 TEST
-rwxrwxr-- 1 root root 2.2K Apr 23 15:11 test.py
```

### 问题一 容器重启之后无法连接？

容器重启之后ssh服务被中断，需要手动重启，打开portainer输入以下命令：

```bash
 service ssh restart
```

即可重新连接。

![image-20240426194356414](https://s2.loli.net/2024/04/26/uPmNqxek4pWlFCa.png)



### 问题二 创建一个Docker失败之后再次连接相同端口为什么不行？

因为ssh有校验措施，只能连接到同一台物理设备，当你的设备失效以后就无法使用，删除过去的key即可重新连接：

<img src="https://s2.loli.net/2024/04/01/yJDmalXE2xfwLR6.png" alt="image-20240401160821511" style="zoom:80%;" />

### 问题三 关闭了VSCode代码就停止运行了？

实际上是这样的，SSH需要保持连接才能运行，不过不要紧我们使用命令来将代码任务注册到后台来持久运行，这样你的代码只需要在默认模式下调整好之后，在再使用如下命令即可保持运行：

当我们运行的代码的时候实际上就是在命令行中调用了命令：

![image-20240401192019401](https://s2.loli.net/2024/04/01/fdlr9SjuFY2qiZx.png)

我们需要将这个命令和nohup来结合实现运行不掉线：

同见问题五正确更新

按方向键盘的上键可以找到上一次运行的命令，当然运行之前要确认当前目录在哪里，是不是你想要的目录？

```bash
(base) root@77d5769f235a:/# cd /home/Share_Space/                          
(base) root@77d5769f235a:/home/Share_Space# ls
data  test.py
(base) root@77d5769f235a:/home/Share_Space# /opt/conda/bin/python /home/Share_Space/test.py
```

下面我们来使用nohup将任务保持在后台，格式如下：

```sh
nohup 你的运行命令 > output.file 2>&1 &
```

结合上边的命令，效果如下：

```bash
nohup /opt/conda/bin/python /home/Share_Space/test.py > output.file 2>&1 &
```

我们来运行一下：

![image-20240401195117010](https://s2.loli.net/2024/04/01/5ui9UFh6DjNaqpd.png)

同时可以看到，这里有了对应的输出文件，输出文件保存了所有输出：

![image-20240401195140188](https://s2.loli.net/2024/04/01/9tsvoN1ZgQEAajJ.png)

<img src="https://s2.loli.net/2024/04/01/5ZuA7HsbzYXVJD3.png" alt="image-20240401195202219" style="zoom: 80%;" />

当然运行完成之后文件显示的更加准确~

### 问题四 Bad owner or permissions on

如果出现**Bad owner or permissions on C:\\Users\\Administrator/.ssh/config > 过程试图写入的管道不存在。 >**的问题，该问题是config文件权限高，vscode不能修改造成的，两种解决办法第一种是修改原来C:盘的config文件权限；第二种是在其他盘新建一个config文件，用于存储远程连接用户，地址等基本信息。

**第一种解决办法**

a.找到.ssh文件夹。它通常位于C:\Users

<img src="https://pic2.zhimg.com/80/v2-00510cb6ff02667327ab74cf045e0475_720w.webp" alt="img" style="zoom:80%;" />

b.右键单击.ssh文件夹，然后单击“属性”，选择“安全”

<img src="https://pic4.zhimg.com/80/v2-403dcc44b073590af68d8350a8d29733_720w.webp" alt="img" style="zoom:80%;" />

c.单击“高级”。 单击“禁用继承”，单击“确定”。 将出现警告弹出窗口。单击“从此对象中删除所有继承的权限”。

d.此时所有用户都将被删除。添加所有者。在同一窗口中，单击“编辑”按钮，单击“添加”以显示“选择用户或组”窗口。

<img src="https://pic1.zhimg.com/80/v2-830693015d51071061ff59f4f57d0220_720w.webp" alt="img" style="zoom: 80%;" />

e.单击“高级”，然后单击“立即查找”按钮。应显示用户结果列表。 选择用户帐户。

<img src="https://pic2.zhimg.com/80/v2-c68f57ffd916d49e56d985c730ccd039_720w.webp" alt="img" style="zoom:80%;" />

f.后面一路点击确定便可。

**记得重启电脑来刷新设置。**

**第二种解决办法**

在除了C盘以外的文件夹新建config文件（空的就可以），在romote-SSH插件的扩展设置中，修改config文件的路径。

![image-20240326115205575](https://s2.loli.net/2024/03/27/xgGATnRrw852BNW.png)

### 问题五 Python脚本使用nohup运行时，指定文件没有输出内容

**此问题由戴珏泓发现**

实际上为Python的缓存机制带来的问题，log存在缓存区中没有及时更新：

可以看到运行之后没有对应的输出;

<img src="https://s2.loli.net/2024/10/21/OfvYEPxVqanH67c.png" alt="image-20241021235853305" style="zoom: 80%;" />

解决方法：

cd进入脚本所在目录执行nohup命令，在命令的python路径与脚本路径之间加-u，强制输出不通过缓存直接打印

例如：

```shell
nohup /opt/conda/bin/python /home/Share_Space/metrics_ml/GA_xgboost.py 2>&1  &
```

改为：

```shell
nohup /opt/conda/bin/python -u /home/Share_Space/metrics_ml/GA_xgboost.py 2>&1  &
```

### 问题六 R_studio打开之后没有见对应这两个文件夹

如打开之后没有见到这两个文件夹，可以先将默认目录设置到~

使用命令：

```R
setwd("~")
```

效果如图：

![image-20241127114234957](https://s2.loli.net/2024/11/27/ZcEwh3GUDMXkOKj.png)

然后刷新一下页面，一定要刷新一下。然后结果：应该是这样：

![image-20241127114538542](https://s2.loli.net/2024/11/27/MLvFi62cVBoDnwu.png)

如果还不行，可以打开命令行使用如下命令：

![image-20241023232258889](https://s2.loli.net/2024/10/23/gHJXbLxNn9UBS4d.png)

使用命令：

```bash
# 创建从 /home/user1/host 到 /home/host/R_Share/user1 的软链接
ln -s /home/host/R_Share/user1 /home/user1/host

# 创建从 /home/user1/Datasets 到 /home/host/Datasets 的软链接
ln -s /home/host/Datasets /home/user1/Datasets
```

user1需要替换的你的用户名，比如我的是Neo：

<img src="https://s2.loli.net/2024/10/23/JjGr1sMXhbpDVWQ.png" alt="image-20241023232512786" style="zoom:67%;" />

### 问题七 创建容器提示Code Failed 500 代码错误

排查是否有以下几个问题：

①这一步中，需要修改的名字是否和你的账号名相同（大小写也要一致）

②端口是否和其他人的重复了？（其他人的端口号可以在Container界面右滑看到）

![image-20250926001725780](https://s2.loli.net/2025/09/26/HAvLQrJxMESh42e.png)

③检查内存设置是否合理，镜像是否有选择对

不要偷懒，直接复制这个错误的镜像号码，正确的镜像一般以bionet或者neonexus开头，下边是一个错误示范：

![image-20250925235955883](https://s2.loli.net/2025/09/25/3TsF1QlIkbLBK2D.png)

