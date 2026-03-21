---
title: BionetServer-No1. Q&A-Docker
categories:
  - Bionet
tags:
  - Bionet
  - ops
  - Q&A
date: "2026/03/18 19:31:36"
---

# BionetServer-No1. Q&A-Docker

<img src="https://s2.loli.net/2024/09/29/qWjluktz8hxAV4E.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727547630579" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

Version:1.1


Date: 2026.03.18

Authors：NeoNexus, dlq

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

考虑是否有端口冲突，网络问题等，排查后仍无法连接请联系我

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

### 问题六 创建容器提示Code Failed 500 代码错误

排查是否有以下几个问题：

①这一步中，需要修改的名字是否和你的账号名相同（大小写也要一致）

②端口是否和其他人的重复了？（其他人的端口号可以在Container界面右滑看到）

![image-20250926001725780](https://s2.loli.net/2025/09/26/HAvLQrJxMESh42e.png)

③检查内存设置是否合理，镜像是否有选择对

不要偷懒，直接复制这个错误的镜像号码，正确的镜像一般以bionet或者neonexus开头，下边是一个错误示范：

<img src="https://s2.loli.net/2025/09/25/3TsF1QlIkbLBK2D.png" alt="image-20250925235955883" style="zoom:50%;" />

### 问题七 如何给conda配置国内镜像源

1. 切换容器的目录到`/root`下

   <img src="https://s2.loli.net/2024/04/01/Wom9RfSLKHzdqnl.png" alt="image-20240401165118181" style="zoom:80%;" />

2. 在容器的`/root`目录下新建`.condarc`文件

<img src="https://s2.loli.net/2024/04/01/5oOPIieFVp7Xvm2.png" alt="image-20240401165149467" style="zoom: 67%;" />

3. 复制以下内容到`.condarc`文件中并保存：

```bash
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  deepmodeling: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/
```

4. 使用以下命令清楚缓存（想要恢复默认也使用这个）

```bash
conda clean -i
```

5. 用 `conda info` 检查配置是否生效，确认 `channel URLs` 已经变成清华源地址。结果应该如下所示：

![image-20240401165401775](https://s2.loli.net/2024/04/01/L9oMIZgW6JEk1U5.png)

### 问题八 如何开启容器的免密登陆

开启免密码登录方式如下：

首先打开你的ssh配置文件：

<img src="https://s2.loli.net/2024/04/03/dgPwOnKMThcuGAq.png" alt="image-20240403170216864" style="zoom:67%;" />

打开之后我们先放这里，等下再用。我们把这个路径叫做SSH配置路径，一定要记住这个路径，可以截图。

我们在windos文件管理器中打开这个文件夹：

<img src="https://s2.loli.net/2024/04/03/v9sV2CIUkceziL1.png" alt="image-20240403170412513" style="zoom:80%;" />

这里分为两种情况，一种是你已经有了上述`id_rsa.pub`文件，如上图红框所示。

另一种情况是没有这个文件，如下所示：

<img src="https://s2.loli.net/2024/04/03/z48qTZJ75yNMaoL.png" alt="29c9f2a1ef1c58e7deeae3ac742c00f" style="zoom:80%;" />

如果你没有，我们打开本地的系统命令行：

<img src="https://s2.loli.net/2024/04/03/KenW8bEy1gwz9UI.png" alt="image-20240403170517802" style="zoom:80%;" />

输入以下内容：

```bash
ssh-keygen
```

<img src="https://s2.loli.net/2024/04/03/ivOr8YQgDAbcLq2.png" alt="image-20240403170800926" style="zoom:80%;" />

这里会提示让你输入密钥文件保存在哪里，这里就要放在你刚才的那个路径的文件下面，就是在vscode设置中打开的路径。记住是文件夹，不是文件，文件夹通常名字为：".ssh"。

输入之后一路回车下去直到看到这个图形生成：

<img src="https://s2.loli.net/2024/04/03/WmeMaujYiO9Ix6G.png" alt="image-20240403171025319" style="zoom:67%;" />

这里就结束了，然后我们回过头打开这个文件，使用vscode或者记事本都可以，同时我们也打开远程服务器的这个目录下的文件：

这路径全名是：

```bash
/root/.ssh/authorized_keys
```

<img src="https://s2.loli.net/2024/04/03/fHrEOVRzybCqh3W.png" alt="image-20240403171155723" style="zoom:67%;" />

打开之后我们将刚才打开的`id_rsa.pub`文件中的内容复制过来，通常如下图所示：

<img src="https://s2.loli.net/2024/04/03/xXeDynObsMYK9Ez.png" alt="image-20240403171427044" style="zoom: 67%;" />

然后我们回到最初打开的ssh配置文件，在你的电脑SSH配置路径上，如果没打开的话，我们还是点击这里打开：

<img src="https://s2.loli.net/2024/04/03/6TcAB2P7SVDQUuC.png" alt="image-20240403171545385" style="zoom: 67%;" />

添加一行内容：

```bash
IdentityFile "C:\Users\NeoNexus\.ssh\id_rsa"  这里的路径要修改成你的配置路径，要记得这里的id_rsa文件并不带pub后缀因为这是私钥
```

添加后效果如下：

![image-20240403171709698](https://s2.loli.net/2024/04/03/sVCgWRw4FJBLZtj.png)

记得Ctrl+S保存修改

重启VSCode就可以愉快免密码直接使用了

### 问题九  VScode 代码补全失效

Vscode代码补全静态检查等依赖于其对应的语言插件

手动开启可以尝试：

![image-20240402224047196](https://s2.loli.net/2024/04/02/bzmETWanVH6YZ49.png)



