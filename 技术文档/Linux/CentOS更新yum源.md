---
title: "CentOS更新yum源-阿里云开发者社区"
source: "https://developer.aliyun.com/article/319268"
author:
published:
created: 2026-11-10
description: "本教程提供CentOS 5/6/7各版本的一键配置命令与详细步骤，助您快速将yum源更换为高速阿里云镜像，彻底解决官方源下载更新缓慢的问题。"
tags:
  - "clippings"
---
## CentOS更新yum源

## 1、备份

`mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup`

## 2、下载新的CentOS-Base.repo 到/etc/yum.repos.d/

> - CentOS 5

```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
```

> - CentOS 6

```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
        
          
        
        
        
          
          AI 代码解读
```

> - CentOS 7

```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
        
          
        
        
        
          
          AI 代码解读
```