---
title: "VMware 安装 CentOS 7（2023 热乎的教程）_vmware centos7"
source: "https://blog.csdn.net/m0_51913750/article/details/131594908"
author:
  - "[[mortalོ]]"
published: 2023-07-07
created: 2026-11-05
description: "文章浏览阅读1.6w次，点赞43次，收藏100次。本文详细介绍了如何下载CentOS镜像文件，使用VWware安装虚拟机，以及在安装CentOS7后如何配置网络，包括选择NAT模式，设置DHCP自动获取IP，并验证网络连通性。"
tags:
  - "clippings"
---
## 1\. 下载 CentOS 镜像文件

- 下载网址： [阿里云开源镜像站](http://mirrors.aliyun.com/centos/7/isos/x86_64/)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4fb2097817aba6ac8b8aaafb69381677.png)

- 小提示：直接用浏览器下载速度可能很慢，建议大家将上面的下载地址复制到【迅雷】进行下载，这样会快很多，下面是详细的步骤：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4510b092abdcde0b2c260caaa0752ab2.png)

- 上述 3 步完成后，就可以点击【立即下载】进行下载了。

## 2\. 安装虚拟机

- 打开 VWware，鼠标左键点击左上角的【文件】，然后点击【新建虚拟机】（此步骤的快捷键为 `Ctrl + N` ）。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0e315c481d5c3749329a09ad35c970c4.png)

- 注：还有一种情况就是：在此之前，你的 VMware 可能没有安装过任何虚拟机，你的界面可能是下面这样，此时你就可以直接点击【创建新的虚拟机】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/18f74bfbf012e6c2aea23b12001acba6.png)

- 勾选【典型】，然后点击【下一步】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e6508b2bafd197a05bd46fb089a3a5fd.png)

- 勾选【稍后安装操作系统】，然后点击【下一步】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3af820293b72f2a506e15e77276c9f76.png)

- 操作系统选择【Linux】，版本选择自己镜像文件的版本（我的是【CentOS 7 64位】）。然后点击【下一步】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f488e285d06e59b8f6d6d90c871bc354.png)

- 【虚拟机名称】可以自己改，然后点击【浏览】选择安装路径（个人自己选择），最后点击【下一步】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a61f347afd559c90d86f31df8000ab55.png)

- 【最大磁盘大小】可以选择默认的 `20` ，也可以根据自己的需求改。然后勾选【将虚拟磁盘拆分为多个文件】（默认就是这个），最后点击【下一步】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/eab722acc893d5ea89e7aa6b23aab179.png)

- 点击【完成】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1a47c681ce9a277b27398681c3ecaf4a.png)

- 回到下面这个界面：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/de859181f92065eb9395a1c74714f4bb.png)

## 3\. 安装 CentOS 7

- 鼠标左键单击【CentOS 7 64位】，也就是上一步创建的虚拟机的名称。然后点击【CD/DVD（IDE） 自动检测】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b608abaaa496aed0b80a7af077bca079.png)

- 勾选【使用 IOS 映像文件】，然后点击【浏览】，选择第一步下载的 CentOS 镜像文件，最后点击【确定】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/bb67b826de584abd9649a9ca996f1865.png)

- 鼠标左键单击【CentOS 7 64位】，也就是上一步创建的虚拟机的名称。点击【开启此虚拟机】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/044151d05fa5e61863496ed7b9e2677a.png)

- 然后就会出现下面这个界面，我们需要等待一段时间：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4ee8f02a1494f6578f27ec3ab26840b2.png)

- 一段时间的等待之后，会出现下面这样的界面。选择语言，我个人强烈建议选择英文，因为后面你用 Linux 命令的话会方便很多，然后点击【Continue】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e36843bd452588e8a70e323b5c919c2a.png)

- 选择【SYSTEM】下的第一个【INSTALLATION DESTINATION】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8ecf8d3b339ed05857812e2a3f771276.png)

- 默认即可，然后点击【Done】，如下如所示：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a1fccfefcbe7de5a41587182c4ec7fab.png)

- 上一步完成之后，会回到下面的界面，然后点击【SOFTWARE】下的【SOFTWARE SELECTION】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7f3e4d52236c19b150ac6810b2f76a55.png)

- 对于初次接触该系统的，建议选择图形化界面，选择之后点击左上角的【Done】。不过界面一般对于我们来说用处不大，而且 CentOS 的界面不好操作，所以这里选择最小安装：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b9e0ab1ee86e5c53995d3c82da51400c.png)

- 选择日期和时间，点击【DATE & TIME】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/80cb47808abc125e1ef0102a1f0ee5fe.png)

- 选择【shanghai】，然后点击左上角的【Done】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/39ad64b5555fd58b866766dd54739fe5.png)

- 点击【Begin Installation】，也就是开始安装：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8135f73dbeb1a6012cd190d8b709c2d2.png)

- 设置 `root` 账户密码，点击【ROOT PASSWORD】：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/46831afb1ff72236aeafc971876ca086.png)

- 设置密码：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f4c640ad80f54e759a1cba722115be73.png)

- 密码设置完成后，进入下面这个界面，等待一会即可：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/eec065c44e1a5880236583e086cd6c88.png)

- 一段时间后，回到下面这个界面，点击右下角的【Finish configuration】（有时候可能没有这个界面，直接看下一步即可）：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/bcf5a57609c384fe1f6446f3ed9a1e7d.png)

- 点击右下角的【Reboot】，也就是重启：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4ce343c1406aa9c9b9127418ccbb51a6.png)

- 输入账户名和密码，密码不可见，然后点击回车：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7b897d677d7a21f2426418f4ecbcdde9.png)

- 输入完成后，进入命令行模式：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a6387e3ce1a397f27ce3b2dd19d9e40f.png)

## 4\. 配置网络

- 因为在安装的时候设置的网络适配器模式为 `NAT` 模式。这里有两种方式进行配置，一种是自动获取 IP，另一种是固定获取 IP。（下面这种方法是自动获取 IP）
- 首先要确保的是 CentOS 为 `NAT` 模式：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b7feda076ccdf894d387d4959782ef25.png)

- 在 VMware 界面点击【编辑】（Edit）里面的 【虚拟网络编辑器】（Virtual Network Edito）：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/82739bea79afc74eba650386b64e4ba4.png)

- 点击【NAT 设置】，然后修改与子网 IP 同网段下的网关 IP，就是前三位必须相同，即 `192.168.138` 要相同，最后一位不同即可（默认就行），最后点击【确定】，保存设置：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ca7315d31ddc7a7599a7cab8b1ebbd6e.png)  
【说明】：上面的步骤正常情况下可以跳过，我们只是做了检查而已。如果你的步骤是严格按照前面示例的那样，上面这几个步骤是不会有问题的，所以可以跳过。

- 进入虚拟机的网络配置文件目录，命令为 `cd /etc/sysconfig/network-scripts/` ，并且用 `ls` 命令查看是否有 `ifcfg-XXX` 名称的配置文件，如果没有则说明网卡没有被识别，只能重装或者换虚拟机版本（正常情况下都是有的，不用担心）：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b7cd4e47ac2fc1ac71aa73c1e8ef5a0b.png)

- 使用 `vi ifcfg-ens33` 命令进入 `ifcfg-ens33` 文件。 按 `i` 进入 `insert` 编辑模式。  
	将 `BOOTPROTO` 设为 `dhcp` ，将 `ONBOOT` 设为 `yes` ，按下 Esc 进入命令模式输入 `:wq` 保存并退出：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/aa635560f563b48f4eeaac66affde142.png)

- 配置完成后输入命令： `service network restart` ，重启网卡让网卡设置生效，之后就可以上网了；
- 输入 `ip addr` 检查下动态分配的 IP，可以发现已经自动分配。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/310e63930b1f48c55b1958750a20dac7.png)

- 最后验证是否能够进行正常上网，输入命令 `ping www.baidu.com` ：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/59dc9d6e5aef0a3d0db2144497d510e8.png)

至此，安装完成！