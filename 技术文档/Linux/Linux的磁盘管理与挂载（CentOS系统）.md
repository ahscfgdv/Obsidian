---
title: "实验二 Linux的磁盘管理与挂载（CentOS系统）_linux_Kangkang12139-openEuler 社区"
source: "https://openeuler.csdn.net/6aa6368861de6467390ede74.html"
author:
published:
created: 2026-11-09
description: "Linux操作系统学习入门（CentOS） Kangkang12139 openEuler 社区"
tags:
  - "clippings"
---

## 实验二 Linux的磁盘管理与挂载（CentOS系统）

**一、实验要求**

1、在虚拟机中添加一块新的虚拟硬盘；

2、使用fdisk，根据需求将虚拟硬盘分区并格式化、挂载；

3、熟悉和磁盘管理相关的命令，如fdisk, mkfs, mount, df等，以及使用/etc/fstab控制分区的自动挂载；

**二、实验内容**

1、打开虚拟机的管理设置，为Centos7添加一块新的虚拟磁盘。

![](https://i-blog.csdnimg.cn/direct/ac75ca8afdff4999b1f96957a6a5401b.png)

![](https://i-blog.csdnimg.cn/direct/d45229b3b1b24c8da44b4da5967a812f.png)

![](https://i-blog.csdnimg.cn/direct/e42c8fe74d9540949215406184781c1b.png)

![](https://i-blog.csdnimg.cn/direct/ad62903850dd4474abc10084109066df.png)

2、查看虚拟机管理设置，新的虚拟磁盘已创建完成。

![](https://i-blog.csdnimg.cn/direct/b9b098f3917a4032a082734d2d675a6b.png)

3、对新创建的磁盘进行格式化、分区和挂载。

（1）使用“fdisk -l”指令查看当前磁盘分区状态,此时可以看到新磁盘/dev/sdb已存在。

![](https://i-blog.csdnimg.cn/direct/e36f3289bcc74ffb9579e1befd6def60.png)

（2）使用“fdisk /dev/sdb”指令开始对磁盘进行分区,根据获取的命令帮助按照需要对磁盘进行相应的操作（一般情况下按默认即可）。

![](https://i-blog.csdnimg.cn/direct/c7a09c49e8ae46c9a48899450db8b18d.png)

![](https://i-blog.csdnimg.cn/direct/49565ea8515a4d46816ac5f9895edb07.png)

（3）再次查看当前磁盘分区状态，此时我们已经将/dev/sdb划分成一个分区，该分区为/dev/sdb1。

![](https://i-blog.csdnimg.cn/direct/882570f9aace4c24b5a50175f6cd9dc4.png)

（4）使用“mkfs -t ext3 /dev/sdb1”指令对分区sdb1格式化成为ext3的文件系统。

![](https://i-blog.csdnimg.cn/direct/b0bf3b44f5234ea999fa5e0d9ee20d19.png)

（5）使用“mkdir /data && mount /dev/sdb1 /data”指令对分区sdb1进行手动挂载，挂载点为/data目录，使用“df -h”指令查看挂载结果，此时sdb1已成功挂载到/data目录上。

![](https://i-blog.csdnimg.cn/direct/7f05c9aa8f7a464ea34f1ef8fa9cb4ba.png)

（6）我们将虚拟机 **重启** 再次查看挂载情况，发现sdb1挂载项目消失。原因是 **手动挂载是临时的** ，Linux的磁盘分区都必须挂载到目录树中的某个具体的目录上才能进行读写操作，在Linux操作系统中由fstab负责配置。 **如果手动挂载不修改fstab文件，那么在开机的时候操作系统会调用fstab配置文件，将手动挂载配置覆盖，这是重启后挂载的分区丢失的根本原因，所以我们需要修改/etc/fstab文件解决这个问题。**

![](https://i-blog.csdnimg.cn/direct/04ecafb3b5ca4060a2324f0009b89d6f.png)

（7）使用“ vim /etc/fstab”指令在fstab配置文件中添加一条有关sdb1的挂载配置。

![](https://i-blog.csdnimg.cn/direct/30dd9654505a47cfa26d27fbb42dcfd0.png)

（8）再次查看磁盘分区的挂载情况，sdb1成功挂载，挂载点为/media目录，至此， **新磁盘分区永久挂载成功。**

![](https://i-blog.csdnimg.cn/direct/2667f193956047ba9abddbb1679d78a1.png)

[openEuler 社区](https://openeuler.csdn.net/)

openEuler 是由开放原子开源基金会孵化的全场景开源操作系统项目，面向数字基础设施四大核心场景（服务器、云计算、边缘计算、嵌入式），全面支持 ARM、x86、RISC-V、loongArch、PowerPC、SW-64 等多样性计算架构

已为社区贡献7条内容

开源官网