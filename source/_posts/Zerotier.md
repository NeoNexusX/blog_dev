---
title: 使用Zerotier实现内网穿透
categories:
  - Bionet
tags:
  - Bionet
excerpt: "some~"
date: 2025/11/28 00:00:00
---

# 使用Zerotier实现内网穿透

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom:67%;" />

由于学校新式vpn软件，不⽀持校内资源映射等操作（需申请），原有基于sslvpn可正常 使⽤，现提供临时的穿透解决⽅案，⽅案基于⾃建zerotier planet服务器，服务器位于⼤陆地 区，通过planet服务器实现点对点连接，带宽⽆上限，取决于⽹络连接状况。

## windows客户端配置

⾸先去 ZeroTier 官⽹下载⼀个 ZeroTier 客户端：

https://www.zerotier.com/download/

根据你的系统选择对应的客户端即可，⽬前⽀持除了ios外的所有主流系统。

<img src="https://s2.loli.net/2025/11/26/e5KFiMm6ESUzQlG.png" alt="image-20251126201514232" style="zoom: 33%;" />

## 替换 planet ⽂件

由于是⾃建planet为了保证安全，屏蔽掉所有官⽅的服务器我们需要替换planet⽂件， planet⽂件上传⾄群内，严禁分享，下载后，找到：

```bash
windows：
C:\ProgramData\ZeroTier\One （这个⽬录是个隐藏⽬录，需要允许查看隐藏⽬录才⾏）

linux：
/var/lib/zerotier-one

macos：
/Library/Application\ Support/ZeroTier/One/
```

在⽂件夹下⾯如果有planet⽂件那说明你找对了复制并替换到这⾥就可以了：

<img src="https://s2.loli.net/2025/11/26/pXNakByZzoJLYuO.png" alt="image-20251126201738485" style="zoom:50%;" />

## 重启zerotier，刷新服务

### windows

<img src="https://s2.loli.net/2025/11/26/BH21LiSKXeZDrQ4.png" alt="image-20251126201859009" style="zoom: 33%;" />

按照名称排序找到 ZeroTier One，并且重启服务：

<img src="https://s2.loli.net/2025/11/26/2ECdkKqm8VFxuny.png" alt="image-20251126201920872" style="zoom:50%;" />

### macos

```zsh
cat /Library/Application\ Support/ZeroTier/One/zerotier-one.pid | sudo xargs kill
```

没有消息就重启完成

## 加⼊自建⽹络

XMU网络id：553d5a85b18f0814

JMU网络id：553d5a85b198d5d7

```bash
windows：
PS C:\Windows\system32> zerotier-cli.bat join 553d5a85b18f0814

macos：
zerotier-cli join 553d5a85b18f0814
```

JMU应加入JMU网络，XMU直接使用命令即可，二者互相独立，完成之后联系我，等待我授权。

## 测试是否成功

完成之后测试⼀下：

打开命令行或者powershell：

```bash
zerotier-cli peers
```

![image-20251126202432706](https://s2.loli.net/2025/11/26/qWlTdSkUbPrX8ih.png)

可以看到PLANET节点，正常，其中117.28.251.155为校内服务器。

```bash
ping 10.11.12.166
```

延迟时间逐渐稳定之后即可，如果测试延迟较⼤，可能不太适⽤。

<img src="https://s2.loli.net/2025/11/26/VbFPQz8dRY7GEZ6.png" alt="image-20251126202511778" style="zoom:50%;" />

XMU组内服务器IP：

```bash
Bionet_No1 [计算服务器] 10.11.12.166
NAS [网络存储服务器] 10.11.12.134
```

连接的时候请使⽤上边的IP，不要使⽤校内IP。

## 注意

zerotier配对需要⼀定时间，重新开机后没有没配对的时候⽆法访问对应的ip，⼀般开机 想⽤的话，可以测试下：

```bash
ping 目标ip
举例：
ping 10.11.12.166
```

直到ping成功了说明配对成功，将zerotier开机⾃启动之后⼀般会⾃⼰配对，⽆需主动管理。如果⻓时间没有配对，请⼿动重启，参考[重启 Zerotier](##重启zerotier刷新服务)。