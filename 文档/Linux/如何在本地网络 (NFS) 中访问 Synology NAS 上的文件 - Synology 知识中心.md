---
title: "如何在本地网络 (NFS) 中访问 Synology NAS 上的文件 - Synology 知识中心"
source: "https://kb.synology.cn/zh-cn/DSM/tutorial/How_to_access_files_on_Synology_NAS_within_the_local_network_NFS"
author:
  - "[[Synology Inc.]]"
published:
created: 2026-01-07
description: "Synology 知识中心为您提供多方面的技术支持，包含常见问题解答、故障排除步骤、软件应用教程以及您可能需要的所有技术文档。"
tags:
  - "clippings"
---
## 如何在本地网络 (NFS) 中访问 Synology NAS 上的文件

## 详情

本文将引导您完成使用 Linux 计算机在本地网络中访问 Synology NAS 的步骤。

## 解决方案

### 在 Synology NAS 上启用 NFS 服务

在使用 NFS 客户端访问共享文件夹之前，您必须将 Synology NAS 上的设置更改为允许通过 NFS 进行共享。请执行以下步骤：

1. 进入 **控制面板** > **文件服务** > **NFS** （适用于 DSM 7.0 及以上版本）或 **SMB/AFP/NFS** （适用于 DSM 6.2 及以上版本）。
2. 勾选 **启用 NFS 服务** 。 <sup><a href="https://kb.synology.cn/zh-cn/DSM/tutorial/How_to_access_files_on_Synology_NAS_within_the_local_network_NFS#x_anchor_id7">1</a></sup>
3. 单击 **应用** 以保存设置。

### 为共享文件夹分配 NFS 权限

在使用 NFS 客户端访问任何共享文件夹之前，您必须先配置要访问的共享文件夹的 NFS 权限。请按以下步骤更改 Synology NAS 上共享文件夹的 NFS 权限：

1. 进入 **控制面板** > **共享文件夹** 。
2. 选择要使用 NFS 客户端访问的共享文件夹，然后单击 **编辑** 。 <sup><a href="https://kb.synology.cn/zh-cn/DSM/tutorial/How_to_access_files_on_Synology_NAS_within_the_local_network_NFS#x_anchor_id30">2</a></sup>
3. 进入 **NFS 权限** 并单击 **创建** 。
4. 请参阅 [本文](https://www.synology.cn/knowledgebase/DSM/help/DSM/AdminCenter/file_share_privilege_nfs) 以编辑权限设置。
5. 单击 **保存** （适用于 DSM 7.0 及以上版本）或 **确定** （适用于 DSM 6.2 及以上版本）以保存规则。
6. 单击 **保存** （适用于 DSM 7.0 及以上版本）或 **确定** （适用于 DSM 6.2 及较早版本）以应用 NFS 权限。
7. 应用 NFS 权限后，您可以在 **NFS 权限** 选项卡的左下角找到共享文件夹的装载路径。装载路径应采用以下格式： `/[*存储空间名称*]/[*共享文件夹名称*]`

### 在客户端通过 NFS 装载共享文件夹

完成上述步骤后，即可使用 NFS 客户端装载共享文件夹。下面我们将演示如何使用 Linux 访问共享文件夹。

1. 在 Linux PC 上打开命令控制台。
2. 在继续安装之前先安装必要的组件。
- Ubuntu <sup><a href="https://kb.synology.cn/zh-cn/DSM/tutorial/How_to_access_files_on_Synology_NAS_within_the_local_network_NFS#x_anchor_id20">3</a></sup>  
	`sudo apt update`
	`sudo apt install nfs-common`
	- CentOS/Redhat/Fedora  
	`sudo yum install nfs-utils`
4. 输入以下 **mount** 命令以在客户端通过 NFS 装载共享文件夹： <sup><a href="https://kb.synology.cn/zh-cn/DSM/tutorial/How_to_access_files_on_Synology_NAS_within_the_local_network_NFS#x_anchor_id8">4</a></sup>  
	`sudo mount -t nfs [*Synology NAS IP 地址*]:[*共享文件夹挂载路径*] /[*NFS 客户端上的挂载点*]` <sup><a href="https://kb.synology.cn/zh-cn/DSM/tutorial/How_to_access_files_on_Synology_NAS_within_the_local_network_NFS#x_anchor_id9">5</a></sup>
- 示例：  
	`sudo mount -t nfs 196.168.xx:/volumeX/test /mnt`
6. 输入 `disk free` 命令以确认您已成功装载共享文件夹。 **文件系统** 列中的输出应采用以下格式：
	`[*Synology NAS IP 地址*]:[*共享文件夹挂载路径*]`  
	`df`

备注：

1. Synology NAS 默认支持 NFSv2 和 NFSv3。您可以决定是启用 NFSv4 还是 NFSv4.1（可用性取决于您的产品型号）。若要启用此选项，请勾选 **启用 NFSv4 支持** 、 **启用 NFSv4.1 支持** 或 **启用 NFSv4 和 NFSv4.1 服务** 。有关详细步骤和更多信息，请参阅 [本文](https://www.synology.cn/knowledgebase/DSM/help/DSM/AdminCenter/file_winmacnfs_nfs) 。
2. 有关不允许通过 NFS 访问的共享文件夹的列表，请参阅相应的帮助文章：
	- 对于 DSM 7.0 及以上版本：“若要启用 NFS 服务”下的 [限制](https://kb.synology.cn/DSM/help/DSM/AdminCenter/file_winmacnfs_nfs?version=7) 区域。
	- 对于 DSM 6.2 及更低版本：“若要启用 NFS 服务”下的 [注](https://kb.synology.cn/DSM/help/DSM/AdminCenter/file_winmacnfs_nfs?version=6) 部分。
3. `apt` 与较新版本的 Linux 兼容。如果 `apt` 不起作用，请将 `apt` 替换为 `apt-get` 并再试一次。
4. 如果无法装载共享文件夹，请检查防火墙设置并确保用户帐户对要映射的共享文件夹具有足够的访问权限。
5. 挂载时，可以在 mount 命令中添加参数 `-o vers=2` 、 `-o vers=3` 或 `-o vers=4` 来指定应使用的 NFS 版本。