---
title: BionetServer-No1使用说明 维护篇
---

# BionetServer-No1使用说明

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

<!-- toc -->

#### R不同版本的部署

R不同于python，没有强烈的显卡依赖，并几乎没有环境冲突，且R有单独用户管理，每个R用户的包各自独立，这样我们部署R的思路就发生了变化，我们按照R的版本来构建容器，每一个容器允许多个用户运行，R可以同时部署多个版本到服务器上，这里举一个例子创建一个4.2的版本。

所有版本的R的官方镜像，都在这里：

[RStudio_DockerHub](https://hub.docker.com/r/rocker/rstudio/tags)

我们查找4.2.2版本：

<img src="https://s2.loli.net/2024/04/01/4PitZcIGm1kRbU2.png" alt="image-20240401195723077" style="zoom:80%;" />

复制这一部分：

<img src="https://s2.loli.net/2024/04/01/n7x8hMc6JoVCYtu.png" alt="image-20240401195801171" style="zoom:80%;" />

来到容器创建页面：

<img src="https://s2.loli.net/2024/04/01/p8FY1AounsS5xJD.png" alt="image-20240401195946708" style="zoom:80%;" />

举个例子：XJJ_R422 意思是 创建人是XJJ 使用的是R4.2.2版本。

网络端口映射如下图：

容器内的R默认是8787我们将其映射到10088端口来使用。

<img src="https://s2.loli.net/2024/04/01/FtDU4pOV96uJqoG.png" alt="image-20240401200140600" style="zoom:67%;" />

高级选择部分，只需要选择一个：

<img src="https://s2.loli.net/2024/04/01/ARPrfnvZ4YjO923.png" alt="image-20240401200302631" style="zoom:67%;" />

空间映射如下图所示：

<img src="https://s2.loli.net/2024/04/01/bBn1L9DXQ4uaRHs.png" alt="image-20240401204447279" style="zoom:67%;" />

和Python部分一致，请参考Python部分即可。然后点击部署即可。

由于里面还没有任何用户，Rstudio不允许root用户登录我们只能通过脚本创建用户来登录，脚本已经写好，但是需要注意的是，只能用来创建初始用户，具体的内容可以看脚本的注释，我们通过shell来运行一下，随便开一个用户的桌面：打开shell：

执行以下命令：

```bash
Neo@Bionet:~/Desktop$ docker ps -a 
CONTAINER ID   IMAGE                                            COMMAND                  CREATED         STATUS                           PORTS                                                                                            NAMES
eebab0f899d0   rocker/rstudio:4.2.2                             "/init"                  9 minutes ago   Up 9 minutes                     0.0.0.0:10088->8787/tcp, :::10088->8787/tcp                                                      XJJ_R422
77d5769f235a   bionet/cuda12_cudnn8_miniconda_ub22.04s:latest   "/bin/bash /start_ss…"   4 hours ago     Up 4 hours                       0.0.0.0:10086->22/tcp, :::10086->22/tcp                                                          sxh123
ea1a28e42060   portainer/portainer-ce:latest                    "/portainer"             23 hours ago    Up 23 hours                      0.0.0.0:8000->8000/tcp, :::8000->8000/tcp, 0.0.0.0:9443->9443/tcp, :::9443->9443/tcp, 9000/tcp   portainer
dbbb1cd0484f   rocker/rstudio:4.2.2                             "/init"                  5 days ago      Up 5 days                        0.0.0.0:8787->8787/tcp, :::8787->8787/tcp, 0.0.0.0:8788->22/tcp, :::8788->22/tcp                 R_422
6ee3c0747be2   nvidia/cuda:12.2.2-cudnn8-runtime-ubuntu22.04    "/opt/nvidia/nvidia_…"   6 days ago      Exited (137) About an hour ago                                                                                                    pytorch
Neo@Bionet:~/Desktop$ docker exec -it  eebab0f899d0 /bin/bash
root@eebab0f899d0:/# 
root@eebab0f899d0:/# 
root@eebab0f899d0:/# cd /home/R_Share/
root@eebab0f899d0:/home/R_Share# ls
createuser.sh
root@eebab0f899d0:/home/R_Share# ./createuser.sh 
```

即可完成创建。

维护篇

## 如何创建新的用户

当然为了批量添加用户，方便管理，我还编写了一个脚本，一个脚本来添加用户，并设置其密码，具体使用见下图：

首先打开脚本：

```bash
Neo@Bionet:~/Desktop$ sudo vim /home/some_scripts/createuser.sh 
```

![image-20240326202700233](https://s2.loli.net/2024/03/26/UCDLHn8rzvG7VZa.png)

修改后运行即可：

```bash
 sudo /home/some_scripts/createuser.sh 
```

## 如何备份整个系统

由于Linux本身万物皆文件的设计理念，加上root用户对几乎全部的系统文件都有访问和更改的权限，因此Linux系统的备份和还原其实非常简单，我们直接打包整个根文件系统就可以了。

我们可以使用tar命令来打包并压缩文件系统，不过这里在打包的过程中需要排除一些不需要文件，或者与新系统文件冲突的文件，包括`/tmp`、`/proc`、`/lost+found` 等目录。

找一个你想保存备份文件的目录，运行下面的命令：

```text
tar -cvpzf ubuntu_backup@`date +%Y-%m+%d`.tar.gz --exclude=/proc --exclude=/tmp --exclude=/boot  --exclude=/lost+found --exclude=/media --exclude=/mnt --exclude=/run /
```

我们会得到一个名为`backup.tgz`的压缩文件，这个文件包含我们需要备份的系统的全部内容。

### 还原

如果系统没有出问题可以正常启动的话，那直接在刚刚的压缩包找找到想还原的文件替换就好了。而如果系统无法启动了，或者说想换一块硬盘克隆一样的系统，那么可以按一下步骤操作：

- 重装干净的Ubuntu系统。跟上面介绍的一样，使用U盘给目标磁盘重装一个干净的系统，这一步是为了省去自己分配存储空间和挂载的麻烦，如果你会自己配置的话那也可以不做这一步。
- 再次使用U盘进入系统，这次选择`try ubuntu without installing`，然后可以看到挂载好的刚刚安装了干净系统的另一个盘，我们在这里对盘里的根文件系统进行一些文件的提取：

```text
sudo su

# 在tryUbuntu根目录下有media文件夹，里面是U盘文件夹和新安装的系统文件夹，在在里分别用（U盘）和（UBUNTU）表示
cd /media/（U盘）
mount -o remount rw ./
 
# 将新系统根目录下/boot/grub/grub.cfg文件备份到U盘中
sudo cp /media/(Ubuntu)/boot/grub/grub.cfg ./    
 
# 将新系统根目录下/etc/fstab文件备份到U盘中，fstab是与系统开机挂载有关的文件，grub.cfg是与开机引导有关的文件，所以这一步至关重要
sudo cp /media/(UBUNTU)/etc/fstab ./
 
# 这一步删除新装ubuntu全部的系统文件，有用的fstab及grub.cfg已经备份
cd /media/(UBUNTU)
sudo rm -rf ./*
 
# 将U盘中backup.tgz复制到该目录下并解压缩
cp /media/(U盘)/backup.tgz ./
sudo tar xvpfz backup.tgz ./
 
# 创建打包系统时排除的文件
sudo mkdir proc lost+found mnt sys media
```

这一步完成后，在用我们在新系统中备份的`fatab`及`grub.cfg` 文件去替换压缩包中解压出来的同名文件，`sudo reboot`重启后就发现系统已经恢复到备份时的状态，包括各种框架，环境，系统设置~
