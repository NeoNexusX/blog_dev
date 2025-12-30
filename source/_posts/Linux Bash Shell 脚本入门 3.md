---
title: Linux Bash Shell 脚本入门（3）——Linux常用命令介绍
categories:
  - Linux
tags:
  - Linux
excerpt: "man/cp/tar/ls/ln/cat/tail/head/less command introducation"
date: "2025/03/12 18:37:00"
---

# Linux Bash Shell 脚本入门（3）——Linux常用命令介绍

[TOC]

<!-- toc -->

<img src="https://s2.loli.net/2025/03/12/U4dGpMnVZgOzsvA.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1741776068525" style="zoom:67%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1695021721029" style="zoom: 50%;" />

## 通过man来了解任何命令

man命令可以访问linux系统的手册页，尤其是当你想要弄清命令行参数的时候，就可以查看对应的命令介绍来了解。

这对刚入门的新手是非常友好的，这里简单介绍一下man命令的使用以及效果，man使用格式如下：

```sh
man  command-name
```

我们最常用的ls命令来举例：

```bash
man ls
```

<img src="https://s2.loli.net/2024/04/20/VGBnDJ6O9lhTWHq.png" alt="image-20240420160532398" style="zoom:67%;" />

当使用man手册的时候实际上是linux的pager程序来显示的，分页显示是一种实用工具，能够逐页逐行显示文本，只需要使用**Space**来进行翻页或者**Enter**逐行查看，假设你使用仿真终端支持箭头翻阅,也可以使用**箭头向前或者向后**滚动手册页的内容，至于什么是仿真终端和真的终端有什么区别，详见上一篇。

向下翻页我们可以看到**DESCRIPTION**这一小节，浏览手册不是按部就班的学习而是作为快速参考来使用的，**DESCRIPTION**小节描述了这个命令的一般性描述。每一小节都有一定的内容，我们常用的小节有：

- SYNOPSIS：命令的语法
- DESCRIPTION ：命令的一般性描述
- OPTIONS ： 命令选项的描述

一般来说命令的使用遵循：

```bash
COMMAND-NAME [OPTION] ... [ARGUMENT]...
```

OPTION 是用于修改命令的行为选项，可添加的OPTION 通常不只有一个，`[ ]`表明这内容不是必须的，`...`表明可以一次指定多个OPTION， `[ARGUMENT]`是传递给命令的参数，以指明命令的操作对象，从中括号可以看出，ARGUMENT也不是必选的，同时可以指定多个ARGUMENT来使用。

为了完整介绍命令的格式，我们再找来tar来举个例子，tar是用来归档和压缩的命令：

```
tar {A|c|d|r|t|u|x}[GnSkUWOmpsMBiajJzZhPlRvwo] [ARG...]
```

`{ }`这一部分是必须的，大括号 `{ }` 通常表示一组选项或者占位符，用于表示替换选项或参数的选择，所以使用的时候必须要带上至少一个参数，后边的`[ ]`表明`GnSkUWOmpsMBiajJzZhPlRvwo`是作为可选参数被加入到命令之中的，相对的`A|c|d|r|t|u|x`是必选的，如果仔细看后边的`[ARG...]`也比较神奇，推测省略号是否在外面这两种写法没有太大区别，在全网也没有找到合适的解释，这里就暂且认为二者一致。

### 浏览文件系统

我们使用ls的时候常常只是ls但是默认的ls显示的内容常常具有误导性，我们举个例子：

```bash
Neo@Bionet:~$ ls
 cudnn-local-repo-ubuntu2204-9.0.0   Downloads   Public            snap              thinclient_drives
 Desktop                             Music       PycharmProjects  'Sunlogin Files'   Videos
 Documents                           Pictures    Share_Space       Templates         WorkSpace

Neo@Bionet:~$ ls -aFl
total 1420
drwxr-x--- 41 Neo  Neo    4096  4月 20 14:43  ./
drwxr-xr-x 45 root root   4096  4月 18 19:03  ../
drwxrwxr-x  3 Neo  Neo    4096  3月 25 12:27  .anaconda/
drwxrwxr-x  3 Neo  Neo    4096  3月 25 12:03  .astropy/
-rw-------  1 Neo  Neo   27327  4月 20 16:09  .bash_history
............................etc
-rw-r--r--  1 Neo  Neo   24921  4月  3 15:03  .xorgxrdp.18.log.old
```

ls带参数和不带参数二者有巨大的区别，我们通过man可以看到参数的意思如下：

```bash
   -a, --all
          do not ignore entries starting with .
          
   -F, --classify
          append indicator (one of */=>@|) to entries
          
   -l     use a long listing format
   
   -h, --human-readable
          with -l and -s, print sizes like 1K 234M 2G etc.
```

- F 会在目录名之后添加/。在可执行文件之后添加*，以帮助用户区分对应的内容，但是对于彩色终端不同类型的文件有不同的颜色，所以这个可以省略
- a 会显示隐藏文件
- l 会以长列表的形式列出来所有文件及其信息。
- h 会将显示的大小修改为人类可读的类型

对于使用来说：

```bash
ls -alh
```

已经完全够用，且方便记忆。

#### l的长列表信息

让我们来解析一下这里面的内容：

```bash
drwxrwxr-x  3 Neo  Neo    4096  3月 25 12:27  .anaconda/
```

- 第1字母，d代表了文件的类型，比如目录（d），文件是（-）、链接文件（l）、字符设备（c）、块设备（b）。
- 第2-10的字母和数字，代表了读写权限，这一部分内容会留到权限篇章介绍，具体可先参看下图。
- 第11位的数字，代表了文件的硬链接数目，参看链接小节的内容。
- 后边的一组字符，代表了文件的属主。
- 后边的一组字符，代表了文件的属组。
- 然后是文件的大小，以字节的形式表示，这里表示目录本身的索引信息占据了4096个字节，并不是代表目录占据了4096字节。
- 文件的上次修改时间。
- 文件名或者目录名。

<img src="https://s2.loli.net/2024/03/24/oRCPZhtelAB8xuX.png" alt="image-20240324211953007" style="zoom:80%;" />

#### 过滤输出列表

ls 默认情况下会显示所有非隐藏文件，但是有时候文件太多，我们就需要使用过滤器来实现过滤，过滤器是一个字符串，可用作简单的文本匹配，可将其作为命令行参数，放置在选项之后使用，举个例子：

```bash
Neo@Bionet:~/Desktop$ ls -alh pytorch221_cuda122
-rw------- 1 root root 13G  3月 26 14:02 pytorch221_cuda122
```

其基本格式如下：

```bash
ls -alh Filename
```

当然其威力不仅仅于此，对于filename，其支持标准通配符（wildcard），我们可以尝试如下例子：

```bash
ls -alh pytor*
```

当然效果上，我们可以看到：

<img src="https://s2.loli.net/2024/04/20/cpw8xHgXouyKflU.png" alt="image-20240420203626775" style="zoom: 80%;" />

也会将对应的文件显示出来，通配符也包括`？、[ ]、!` ,效果如下：

<img src="https://s2.loli.net/2024/04/20/SHL1FUPydMYzukT.png" alt="image-20240420203943906" style="zoom:80%;" />

当然匹配符有很多，这里就不再一一赘述。

<img src="https://s2.loli.net/2024/04/20/Z98N1QBwkSfFdVU.png" alt="image-20240420204333227" style="zoom:80%;" />

```bash
Neo@Bionet:~/Desktop$ ls -alhi
total 13G
26083455 drwxr-xr-x  2 Neo  Neo  4.0K  4月 20 22:23 .
26083329 drwxr-x--- 43 Neo  Neo  4.0K  4月 20 22:28 ..
26088183 -rw-r--r--  1 root root    7  4月 20 21:28 1.txt
26086399 -rw-r--r--  1 root root    7  4月 20 21:33 2.txt
26083364 -rwxrwxrwx  1 root root 1.4K  4月 20 18:39 creatematlab.sh
26088522 -rwxrwxrwx  1 root root  571  4月  1 11:58 c.sh
26084449 -rwxrwxrwx  1 Neo  Neo  1.7K  4月 11 17:16 devel_level.df
26083390 lrwxrwxrwx  1 Neo  Neo    32  4月 20 22:23 ln_createusr -> /home/some_scripts/createuser.sh
26084386 -rw-------  1 root root  13G  3月 26 14:02 pytorch221_cuda122
```

`ls -alhi`中的i显示了inode编号，**文件或目录的inode编号是内核分配给文件系统中每一个对象的唯一标识**。

## 复制文件和目录

cp命令是将文件和目录从文件系统的一个位置复制到另一个位置，其最基本的用法如下：

```bash
cp --help
Usage: cp [OPTION]... [-T] SOURCE DEST
  or:  cp [OPTION]... SOURCE... DIRECTORY
  or:  cp [OPTION]... -t DIRECTORY SOURCE...
Copy SOURCE to DEST, or multiple SOURCE(s) to DIRECTORY.
```

可以看到cp支持的表达式如下：

```bash
cp [OPTION]... [-T] SOURCE DEST
```

通常情况下，如果 `cp` 命令的目标路径是一个目录，则会将源文件复制到目标目录中。但是，如果你希望将源文件复制到一个普通文件中，并且目标路径可能会被解释为目录，那么你可以使用 `-T` 选项来强制 `cp` 将目标路径视为普通文件。

```bash
Neo@Bionet:~/Desktop$ sudo cp -T 1.txt 2.txt
```

需要注意的是这里的的复制指的是将1的内容直接覆盖到2上。如果需要强制询问是否需要覆盖已有文件：

```bash
$ sudo cp -Ti  1.txt 2.txt
cp: overwrite '2.txt'? y
```

回答y之后就会覆盖，任何不是y的回答都取消覆盖。

```bash
cp -Ri Share_Space/ newshare/
```

会直接将`Share_Space/`的内容直接复制到新的文件夹`newshare/`当中，递归的包含所有的内容。

当然cp也支持使用通配符，举个例子，我们使用cp命令将当前目录下的所有sh脚本复制到路径/home/some_scripts/下中：

```bash
:~/Desktop$ sudo cp -i ./*.sh /home/some_scripts/
cp: overwrite '/home/some_scripts/creatematlab.sh'? n
cp: overwrite '/home/some_scripts/createuser.sh'? n
cp: overwrite '/home/some_scripts/c.sh'? n
```

此操作会将桌面的所有sh脚本复制过去，因为我已经提前复制了，所以会一个一个询问是否要覆盖。

## 删除文件和文件夹

linux中最常用且最危险的命令莫过于rm了。在linux中删除被叫做移除（removing）。bash shell 中用于删除文件的命令是rm。rm命令的基本格式非常简单：

```bash
SYNOPSIS
       rm [OPTION]... [FILE]...
```

举个例子，我们查看当前目录下面的内容，并尝试删除一个文件：

![image-20240503220346917](https://s2.loli.net/2024/05/03/CfWec4gY7jP3lDh.png)

可以看到有一个Dockerfile.swp文件这是一个缓存的交换文件，并没有什么用，我们将其删除：

```bash
rm -i .Dockefile.swp
```

删除之后我们可以看到：

![image-20240503220637952](https://s2.loli.net/2024/05/03/bTzZSFigO7I3QaK.png)

此处加入i参数目的是为了提醒自己要删除对应的文件了，shell的删除命令并不存在垃圾箱这种东西，如果一旦不小心删除那么就永远也找不回来了，所以要养成加入i参数的好习惯。

rm的文件参数同时也支持通配符，也就是说可以通过rm一次性删除很多文件，这是非常有用的，当你想删除某一类文件的时候就可以使用：

```bash
rm -i *.sh
```

此操作会删除所有的sh脚本文件。

### 删除文件夹

对于非空目录rmdir命令足以，但是大多数情况下，目录中都有内容，我们就必须使用rm命令来实现了。

```bash
rm -ri 目录名或路径 
```

`-r/R`（没错此处Rr效果是一样的）参数会向下进入到目录中将其内容全部删除，在这个过程中会一个一个询问你是否删除，如果不想这样，且确认数据真的没有用处了，我们可以选择`-rf`参数。

效果如下，有以下文件夹：

![image-20240503223624129](https://s2.loli.net/2024/05/03/mcAO758vHoQZ2gI.png)

使用命令进入删除：

![image-20240503223554291](https://s2.loli.net/2024/05/03/G3W4wgqejYERMJp.png)



## 链接文件

链接文件可以说是linux系统的优势，如果需要在系统中维护同一个文件的两个或者多个副本的时候可以使用单个物理副本+多个虚拟副本(链接)的方法代替创建多个物理副本，链接是目录中指向文件真实位置的占位符，在linux中有两种类型的文件链接：

- 符号链接（软连接）
- 硬链接

创建链接的命令如下：

```bash
Neo@Bionet:~/Desktop$ ln --help
Usage: ln [OPTION]... [-T] TARGET LINK_NAME
  or:  ln [OPTION]... TARGET
  or:  ln [OPTION]... TARGET... DIRECTORY
  or:  ln [OPTION]... -t DIRECTORY TARGET...
In the 1st form, create a link to TARGET with the name LINK_NAME.
In the 2nd form, create a link to TARGET in the current directory.
In the 3rd and 4th forms, create links to each TARGET in DIRECTORY.
Create hard links by default, symbolic links with --symbolic
```

可以看到不仅可以创造针对文件的链接，也可以创造针对目录的链接。

### 符号链接（软连接）

符号链接是一个实实在在的文件，只不过文件的内容是指向的是虚拟文件系统中的另一个地方的文件，这两个以符号方式连接在一起的文件彼此的内容并不相同。

举个例子，我们将刚刚复制的脚本在桌面删除，然后以不同的方式链接回来看看。

```bash
Neo@Bionet:~/Desktop$ sudo rm ./createuser.sh 
Neo@Bionet:~/Desktop$ ln -s /home/some_scripts/createuser.sh ./ln_createusr
# 链接完成之后，显示当前实际上是一个链接文件：
Neo@Bionet:~/Desktop$ ls -alh
lrwxrwxrwx  1 Neo  Neo    32  4月 20 22:23 ln_createusr -> /home/some_scripts/createuser.sh
# 源文件并没有任何变换：
Neo@Bionet:~/Desktop$ ls -alh /home/some_scripts/createuser.sh 
-rwxr-xr-x 1 root root 859  4月 20 22:07 /home/some_scripts/createuser.sh
```

可以看到通过命令：

```bash
ln -s /home/some_scripts/createuser.sh ./ln_createusr
```

我们创造了一个从对应目录下的文件的软连接到桌面的一个新文件叫`ln_createusr`。

使用ls来查看的时候其文件名部分被替换成了`ln_createusr -> /home/some_scripts/createuser.sh`的内容，而不是本身的文件名，这里的 `->`代表了该文件是连接到文件`/home/some_scripts/createuser.sh`的一个符号链接。

我们通过上文中两个 `ls -alh`文件看到，两个文件的大小并不相同，源文件有859个字节，而链接文件只有32个字节

而我们尝试打开链接文件，可以看到：

```bash
Neo@Bionet:~/Desktop$ vim ./ln_createusr 
```

<img src="https://s2.loli.net/2024/04/20/SUrzaQJtpcmqbwk.png" alt="image-20240420224944397" style="zoom:67%;" />

显示内容和源文件保持一致。当我们尝试修改的时候因为源文件权限的问题，无法修改：

<img src="https://s2.loli.net/2024/04/20/YwyBM5mHhdxNeJV.png" alt="image-20240420225031627" style="zoom:67%;" />

当我们修改权限以后：

```bash
Neo@Bionet:~/Desktop$ sudo chmod 777 /home/some_scripts/createuser.sh 
```

文件就可以正常修改了，这里说明链接文件的权限取决于源文件，二者的权限保持一致。

### 硬链接

硬链接创建的是一个独立的虚拟文件，其中包含了原始文件的信息和位置，但是二者本质上是同一个文件，这是和符号链接的最大区别。

我们再举一个例子：同样将上文中复制的文件从桌面删除再以硬连接的方式连接回来。

```bash
Neo@Bionet:~/Desktop$ rm ./creatematlab.sh     
Neo@Bionet:~/Desktop$ sudo ln /home/some_scripts/creatematlab.sh  ln_creatematlab

Neo@Bionet:~/Desktop$ ls -alhi
109051906 -rwxr-xr-x  2 root root 1.4K  4月 20 22:07 ln_creatematlab

Neo@Bionet:~/Desktop$ ls -alhi /home/some_scripts/creatematlab.sh 
109051906 -rwxr-xr-x 2 root root 1.4K  4月 20 22:07 /home/some_scripts/creatematlab.sh
```

通过`sudo ln /home/some_scripts/creatematlab.sh  ln_creatematlab`创建链接之后，我们可以看到：

二者共用一个inode号码，说明二者是一个文件。省略参数的情况下，其意思是默认在当前目录这里创建一个名字一样的硬链接，虽然下图创建失败了，但是其详细展示了创建的内容：

![image-20240503213411568](https://s2.loli.net/2024/05/03/txdviqWJwUNMjRz.png)

### 软连接和硬连接的区别

1. **软连接（Symbolic Link）**：
   - 软连接类似于Windows系统中的快捷方式（Shortcut）。
   - 它是一个特殊的文件，包含了对另一个文件或目录的引用路径。
   - 软连接不依赖于原始文件的文件名，即使原始文件被移动或重命名，软连接依然有效。
   - 软连接可以跨文件系统，即可以链接到不同文件系统中的文件。
   - 删除原始文件会导致软连接失效，因为它只是指向原始文件的一个路径。
2. **硬连接（Hard Link）**：
   - 硬连接直接指向文件的物理位置，即文件的inode节点。
   - 硬连接与原始文件共享同一个inode，因此它们实际上是同一个文件。
   - 硬连接不能跨文件系统，只能在同一文件系统中创建。
   - 硬连接不能链接到目录，只能链接到文件。
   - 删除原始文件不会影响硬连接，因为只要有一个硬连接存在，文件内容就不会被删除。

 硬链接（Hard Link）不会占用两倍的空间。实际上，硬链接与原始文件共享相同的存储空间，它们指向同一个inode节点。这意味着，无论有多少个硬链接指向同一个文件，它们都只占用原始文件所占用的存储空间。

在UNIX和类UNIX系统中，文件的内容和文件的元数据（如文件名、权限、所有者等）是分开存储的。文件的内容存储在数据块中，而元数据存储在inode节点中。硬链接和原始文件都指向同一个inode节点，因此它们共享相同的数据块。



举个例子我想查看如何使用 tar 命令进行归档的压缩和解压就可以使用：

```shell
man tar
```

效果如下：

<img src="https://s2.loli.net/2024/04/20/jqhmYyKxe3lD5gT.png" alt="image-20240420145521695" style="zoom:67%;" />

<img src="https://s2.loli.net/2024/04/20/W4yvErVod9tN58c.png" alt="image-20240420150802190" style="zoom:67%;" />

这里tar的语法描述如下：

```shell
SYNOPSIS
   Traditional usage
       tar {A|c|d|r|t|u|x}[GnSkUWOmpsMBiajJzZhPlRvwo] [ARG...]
       
   UNIX-style usage
       tar -A [OPTIONS] ARCHIVE ARCHIVE
		.......

       tar -x [-f ARCHIVE] [OPTIONS] [MEMBER...]

   GNU-style usage
       tar --create [--file ARCHIVE] [OPTIONS] [FILE...]
		.......

       tar {--extract|--get} [-f ARCHIVE] [OPTIONS] [MEMBER...]
```

这一部分描述了常用的方式：

```bash
Traditional usage
       tar {A|c|d|r|t|u|x}[GnSkUWOmpsMBiajJzZhPlRvwo] [ARG...]
```

参数了解之后，可以看看这些参数有什么用：

关于参数的内容linux的运行手册有的内容非常多而且不一致，我们可以换个方式来快速查看， 大多数命令都支持-h选项来实现，更快速的信息获取：

```shell
Neo@Bionet:~/Desktop$ tar --help
Usage: tar [OPTION...] [FILE]...
GNU 'tar' saves many files together into a single tape or disk archive, and can
restore individual files from the archive.
Examples:
  tar -cf archive.tar foo bar  # Create archive.tar from files foo and bar.
  tar -tvf archive.tar         # List all files in archive.tar verbosely.
  tar -xf archive.tar          # Extract all files from archive.tar.

 Main operation mode:
  -A, --catenate, --concatenate   append tar files to an archive
  -c, --create               create a new archive
      --delete               delete from the archive (not on mag tapes!)
  -d, --diff, --compare      find differences between archive and file system
  -r, --append               append files to the end of an archive
      --test-label           test the archive volume label and exit
  -t, --list                 list the contents of an archive
  -u, --update               only append files newer than copy in archive
  -x, --extract, --get       extract files from an archive

 Operation modifiers:

      --check-device         check device numbers when creating incremental
                             archives (default)
  -g, --listed-incremental=FILE   handle new GNU-format incremental backup
  -G, --incremental          handle old GNU-format incremental backup
      --hole-detection=TYPE  technique to detect holes
      --ignore-failed-read   do not exit with nonzero on unreadable files
      --level=NUMBER         dump level for created listed-incremental archive
      --no-check-device      do not check device numbers when creating
                             incremental archives
```

获取的信息，截取了一部分，这样就避免了翻页程序过于正式的效果，反而看清楚该如何使用命令。

我们找到解压的参数 `-x`作为主参数，同时浏览一下常用的vf参数是什么意思： 

 `-f, --file=ARCHIVE         use archive file or device ARCHIVE`

## 查看文件

查看文件是linux最重要的特性，有了这些查看命令的支持，相较于windows那些操作系统，在有大文件的时候linux不仅能打开，并且能快速查看当中的内容。

当你手头有一个很大的文本文件，你可能会想看一看到底是什么，linux有几个经典的命令来实现这个操作，我们只介绍当中比较常用的命令。

### cat命令

cat命令是最基础的文件探查命令，当你查找资料的时候经常能看到，所以这里介绍一下：

我们有一个test文件里面包含多行的内容：

<img src="https://s2.loli.net/2024/05/03/f6vskCKxOaSPNY9.png" alt="image-20240503230813839" style="zoom: 80%;" />

我们使用cat来查看一下：

```bash
cat -n test
```

效果如下：

```bash
neo@NeoNeuxs:~/Desktop$ cat -n test
     1	123456
     2	12345
     3	1234
     4	123
     5	12
     6	1
     7	
     8	
```

这里就没有什么特别的，其显示的内容就是对应的信息，其中有多种变形的参数，这里`-n`代表显示行号，同时还有`-b`选项只给有文本的行加上行号，效果如下：

```bash
neo@NeoNeuxs:~/Desktop$ cat -b test
     1	123456
     2	12345
     3	1234
     4	123
     5	12
     6	1


neo@NeoNeuxs:~/Desktop$ 
```

对于cat来说当其应对大文件尤其是超大文件的时候他会不停的刷新模拟终端的内容来显示，我们无法查看到底有什么，为了解决这个问题我们可以使用less。

less命令提供了非常多的实用特性，能够实现文本文件中的前后翻动，还有一些高级搜索功能。最主要的是less能够实现在完全读取文件之前，显示文件的部分内容，cat和more是无法实现的，为了验证这个功能我们来找一个大的文件来进行操作，

```bash
neo@NeoNeuxs:~/Desktop/PTM/Dataset/uniprot$ less ./uniprotkb_AND_reviewed_true_AND_model_o_2024_04_25.tsv 
```

uniprotkb是一个蛋白质库，其中记录了许多蛋白质序列，我们使用less来查看里面的内容，显示如下：

![image-20240503232813500](https://s2.loli.net/2024/05/03/abhLBQX83YleS9O.png)

由于数据的每一行都非长的长，当达到屏幕极限的时候就会换行，less是按页来显示的，我们可以通过键盘的pgup、pgdn，或者空格来翻页查看，其加载速度并不取决于文件大小而在于每一页的内容，这样当你使用vcode、等编辑器无法一下打开查看信息的时候就是less大发神威的时候，如下图，此文件达到了100多MB,算是比较小的，但是使用其他文件阅读器打开还是比较慢，less在一瞬间就可以打开，并查看其中的内容。

<img src="https://s2.loli.net/2024/05/03/BlaurX6792TROQf.png" alt="image-20240503233216985" style="zoom:67%;" />

![image-20240503233340449](https://s2.loli.net/2024/05/03/J8RSrN1jftEz9Ls.png)

### 只查看文件部分内容

你需要查看的文件有时候经常位于文件的开头或者结尾，当文件很大的时候，你就只能干等着cat或more来载入整个文件，当然linux也提供了专门的命令来解决这个问题，下面就会介绍 tail、head命令。

#### 查看文件末尾（tail命令）

tail命令会显示文件的末尾的内容，默认情况下会显示文件末尾的最后10行，我们还是使用刚才的文件来检测：

```bash
neo@NeoNeuxs:~/Desktop/PTM/Dataset/uniprot$ tail ./dbPTM_Phosphorylation
ZO3_HUMAN	O95049	919	Phosphorylation	18669648	KKFMRVHDAESSDEDGYDWGP
ZO3_HUMAN	O95049	920	Phosphorylation	18669648	KFMRVHDAESSDEDGYDWGPA
ZO3_HUMAN	O95049	925	Phosphorylation	18669648	HDAESSDEDGYDWGPATDL--
ZRAB2_HUMAN	O95218-2	9	Phosphorylation	18669648	--MSTKNFRVSDGDWICPDKK
ZRAB2_HUMAN	O95218-2	114	Phosphorylation	18669648	GGFNERENVEYIEREESDGEY
ZRAB2_HUMAN	O95218-2	120	Phosphorylation	18669648	ENVEYIEREESDGEYDEFGRK
ZRAB2_HUMAN	O95218-2	153	Phosphorylation	18669648	SILKEVEDKESEGEEEDEDED
ZRAB2_HUMAN	O95218-2	181	Phosphorylation	18669648	EDEDEDDADLSKYNLDASEEE
ZRAB2_HUMAN	O95218-2	183	Phosphorylation	18669648	EDEDDADLSKYNLDASEEEDS
ZRAB2_HUMAN	O95218-2	188	Phosphorylation	18669648	ADLSKYNLDASEEEDSNKKKS
```

`-n`可以显示指定行数，效果如下：

```bash
neo@NeoNeuxs:~/Desktop/PTM/Dataset/uniprot$ tail -n 1 ./dbPTM_Phosphorylation
ZRAB2_HUMAN	O95218-2	188	Phosphorylation	18669648	ADLSKYNLDASEEEDSNKKKS
```

**tail最重要的参数是`-f`其可以实现在线刷新的效果，当有新的数据写到文件的时候会自动刷新。**

#### 查看文件头部（head命令）

head命令的使用方式几乎和tail一样，只不过显示的内容是头部的，相同的命令参数-n也可以指定当前显示的行数。

![image-20240504003518940](https://s2.loli.net/2024/05/04/CqGdyjZbElXwSum.png)
