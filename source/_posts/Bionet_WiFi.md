---
title:  Bionet_WIFI使用指南
categories:
  - Bionet
tags:
  - Bionet
  - A406
  - WIFI
date: "2024/12/31 20:46:25"
---

# Bionet_WIFI使用指南

<img src="https://s2.loli.net/2024/09/29/yhNMvJ21PrfqVOk.jpg" alt="使用指南" style="zoom:67%;" />

<!-- toc -->

适用对象：文宣楼A406课题组成员

A406房间包含两个独立的路由器，分别有1000Mb的带宽。

其SSID（名字）如下：

<img src="https://s2.loli.net/2024/05/12/HDXRUkAf2tINVlp.png" alt="image-20240512112431863.png" style="zoom: 67%;" />

使用的时候，应选择排名比较靠前的WIFI使用，越靠前来说信号越好，速度越快。

## 获取本机的真实MAC地址

路由器开启了连接管控，可以找谭然、或者我来执行绑定和连接。设备绑定依赖于本机的真实MAC地址。首先要获取设备的真实MAC地址，获取方法如下：

### PC设备：

获取本机MAC地址

在设置中查找MAC：

<img src="https://s2.loli.net/2024/05/11/1IlVejHObDtQ6oE.png" alt="image-20240511182342670.png" style="zoom: 67%;" />

点击：

<img src="https://s2.loli.net/2024/09/29/SAQXCqUmkYhjteF.png" alt="image-20240928171559953" style="zoom: 67%;" />

关闭随机硬件MAC地址：

<img src="https://s2.loli.net/2024/05/11/OFXSioyE2GUqvhW.png" alt="image-20240511182513806.png" style="zoom:67%;" />

点击：

<img src="https://s2.loli.net/2024/05/12/ULCXqEJRFigHPmz.png" alt="image-20240511182756107.png" style="zoom:67%;" />

然后记录下此信息：

<img src="https://s2.loli.net/2024/05/12/zKdChegHlj5PfuY.png" alt="image-20240511182816689.png" style="zoom:80%;" />

以如下格式发送给我：

```shell
设备名称：neo_phone (姓名_设备名称)
MAC地址：填入MAC地址
```

然后即可继续连接。

### Android设备：

以荣耀设备为例子：

打开设备设置管理界面：

<img src="https://s2.loli.net/2024/05/11/hJEzybCkvB7SfqP.png" alt="image-20240511183320386.png" style="zoom:67%;" />

在上方搜索栏搜索：MAC

选择：

<img src="https://s2.loli.net/2024/05/11/3oHQGbsweR1YINL.png" alt="image-20240511183449726.png" style="zoom:50%;" />

将此内容截图发给我：

<img src="https://s2.loli.net/2024/05/11/WbVSzdspKXocCL9.png" alt="image-20240511183526491.png" style="zoom: 80%;" />

等我添加信息之后，选择对应的WIFI连接：

连接时选择高级选项，将随机MAC地址修改成使用设备MAC：

<img src="https://s2.loli.net/2024/05/11/QaoEg38j5WfxNYH.png" alt="image-20240511183602054.png" style="zoom:67%;" />

<img src="https://s2.loli.net/2024/05/11/92ESIyW4pxHRz1j.png" alt="image-20240511183706756.png" style="zoom: 50%;" />

输入密码连接即可，等我添加完成设备之后就可以上网了。

### IOS设备：

IOS设备需要先连接到WIFI，点击对应WIFI右侧的感叹号，查看MAC地址信息：

```shell
设备名称：neo_ipad/iphone/MAC (姓名_设备名称)
MAC地址：填入MAC地址
```

<img src="https://s2.loli.net/2024/05/11/YbuERIdAP4XhZOy.png" alt="image-20240511185628123.png" style="zoom:67%;" />

连接之后会提示无法上网，问题不大，关闭自己的私有MAC地址：

<img src="https://s2.loli.net/2024/05/11/FnKaXR76Tm2fpPu.png" alt="image-20240511184700751.png" style="zoom:67%;" />