---
title: "CentOS7配置SSH远程登录_centos7配置ssh跨网段登录"
source: "https://blog.csdn.net/qq_40308101/article/details/108534788"
author:
  - "[[萌翻天]]"
published: 2020-09-11
created: 2026-11-10
description: "文章浏览阅读6.8k次。本文详细介绍如何在Linux系统中安装及配置SSH服务，包括使用yum安装openssh-server，修改sshd_config配置文件允许root登录和密码认证，以及启动、重启网络和设置SSH服务开机启动的方法。"
tags:
  - "clippings"
---
1.安装 openssh-server  
`yum install -y openssl openssh-server`

2.修改 配置文件  
`vim /etc/ssh/sshd\_config`

打开22端口

![](https://i-blog.csdnimg.cn/blog_migrate/69c19a66e4a92f6187a5512e2a426fbf.png)

允许root账户登录

![](https://i-blog.csdnimg.cn/blog_migrate/652887fcc1e13269e1b3b39c4de89f09.png)

允许账号+密码的方式登录

![](https://i-blog.csdnimg.cn/blog_migrate/dcea9a191372f995824d9171591f5f2a.png)

3、

启动ssh服务  
`systemctl start sshd.service`

重启网络  
`service network restart`

设置开机启动ssh服务  
`systemctl enable sshd.service`-