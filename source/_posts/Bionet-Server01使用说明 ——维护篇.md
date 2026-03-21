---
title: BionetServer-No1-Ops
categories:
  - Bionet
tags:
  - Bionet
  - ops
date: "2026/02/12 20:46:25"

---

# BionetServer-No1使用说明——维护篇

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

Version:1.1


Date: 2026.02.03

Authors：NeoNexus

<!-- toc -->

[TOC]

# 维护篇

### 如何创建新的用户

当然为了批量添加用户，方便管理，我还编写了一个脚本来添加用户，并设置其密码，具体使用见下图：

首先打开脚本：

```bash
Neo@Bionet:~/Desktop$ sudo vim /home/some_scripts/createuser.sh 
```

![image-20240326202700233](https://s2.loli.net/2024/03/26/UCDLHn8rzvG7VZa.png)

修改后运行即可：

```bash
 sudo /home/some_scripts/createuser.sh 
```

### 查看当前用户占用空间

```bash
sudo du -h --max-depth=1 /home | sort -h
```

### 查看磁盘占用情况

```bash
df -h
```

### 查看 xrdp报错

```bash
tail -n 50 ~/.xsession-errors
```

举例：

```bash
Tracker-WARNING **: 19:15:58.491: Could not save error report: Failed to write file “/home/Neo/.cache/tracker3/files/errors/4bab830e5f2b923be007acd5ac8e835d.N7VCK3”: write() failed: No space left on device
```

爆空间了，清理磁盘

### 常见磁盘清理目录

```bash
/home/username/.cache
```

### Git配置

```bash
git config --global user.name "Neo"
git config --global user.email "neonexus@foxmail.com"
```

密钥生成：

```bash
 ssh-keygen -t ed25519 -C "neonexus@foxmail.com"
```

密钥获取：

```bash
 cat ~/.ssh/id_ed25519.pub
```

### IB网络带宽测试

TrueNAS：启动服务端

```bash
docker run --rm -it --net=host --privileged \
  -v /dev/infiniband:/dev/infiniband \
  docker.1ms.run/ubuntu:22.04 bash -lc \
  "apt-get update && apt-get install -y perftest && ib_write_bw -R"
```

TrueNAS：启动客户端

```bash
docker run --rm -it --net=host --privileged \
  -v /dev/infiniband:/dev/infiniband \
  docker.1ms.run/ubuntu:22.04 bash -lc \
  "apt-get update && apt-get install -y perftest && ib_write_bw -R 192.168.100.1"
```

Ubuntu启动服务端：

```bash
ib_write_bw -R
```

Ubuntu启动客户端：

```bash
ib_write_bw -R 192.168.100.2
```

### TrueNAS手动配置IB网卡信息：

加载系统驱动：

```bash
sudo modprobe ib_core rdma_cm mlx4_core mlx4_ib mlx4_en xprtrdma rpcrdma svcrdma ib_ipoib
```

查看网卡的代号：

```bash
ip -br link show
```

配置TrueNAS的ip over ib：

```bash
echo connected | sudo tee /sys/class/net/ibp1s0/mode
sudo ip link set dev ibp1s0 mtu 65520
sudo ip link set dev ibp1s0 up
sudo ip addr add 192.168.100.2/24 dev ibp1s0
ip addr show | grep ibp1s0
```

### Bionet NO1的网卡信息：

配置BionetNo1的IP over Ib：

```bash
echo connected | sudo tee /sys/class/net/ibs1/mode
sudo ip link set dev ibs1 mtu 65520
sudo ip link set dev ibs1 up
sudo ip addr add 192.168.100.1/24 dev ibs1
ip addr show | grep ibs1
```

RDMA挂载：

注意此命令应该在NAS启动后再使用，否则会导致无法进入home目录：

```bash
sudo mount -t nfs -o soft,vers=4.2,proto=rdma,port=20049,timeo=20,retrans=10 192.168.100.2:/mnt/Bionet_01/Data /home/Datasets -v
```

### Docker系列命令

BionetNO1容器挂载：

```bash
sudo mount /dev/docker/docker /var/lib/docker/
```

重启docker：

```bash
sudo systemctl restart docker
```

