---
title: "PXE 启动怎么设置：BIOS 开启网络启动与各品牌菜单位置"
source: "https://www.dcim.co/learn/enable-pxe-boot-bios/"
author:
published: 2026-10-04
created: 2026-11-05
description: "PXE 启动要在 BIOS/UEFI 里做三件事：打开网卡的网络引导（Network Stack、PXE Device 或网卡 Option ROM），选 IPv4 或 IPv6，再把网卡排进启动顺序或开机按 F12。附各品牌菜单路径、racadm 批量设置和启动菜单里没有网卡的排查。"
tags:
  - "clippings"
---
PXE 启动需要在 BIOS/UEFI 里打开三处：网卡端口的网络引导功能、与启动模式匹配的 PXE 类型（UEFI 下选 IPv4 或 IPv6 PXE，Legacy 下启用网卡的 PXE Option ROM），以及启动顺序或一次性启动菜单里的网卡项。服务器 PXE 启动不了，绝大多数是这三处有一处没开，或者开在了没接线的端口上。下面先列检查清单，再给各品牌的菜单路径。

## PXE 启动要打开哪几处

| 设置项    | UEFI 模式                                                                        | Legacy 模式                                                                       | 没打开时的表现                    |
| ------ | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- | -------------------------- |
| 网卡本身   | Integrated Devices / Onboard LAN 里该网卡为 Enabled                                 | 同左                                                                              | 系统里也看不到这块网卡                |
| 网络引导驱动 | Network Stack 打开，并打开 IPv4 PXE Support 或 IPv6 PXE Support；Dell 是逐个配置 PXE Device | 网卡端口的 Option ROM 设为 PXE（Dell 叫 Legacy Boot Protocol，超微叫 Onboard LAN Option ROM） | 启动菜单和启动顺序里都没有网卡项           |
| 启动顺序   | UEFI Boot Order 里的 `UEFI: PXE IPv4 ...` 一类条目排在硬盘前，或用一次性启动                      | Boot Sequence 或网络设备优先级里的网卡项排在硬盘前                                                | 有网卡项，但开机直接进了硬盘上的系统         |
| 端口与协议  | 只在接装机网的端口开启，协议与装机网一致                                                           | 只在接装机网的端口开启                                                                     | 逐个端口、逐个协议等超时，很慢或从错误的网络拿到地址 |

一个容易被忽略的细节：UEFI 固件通常在开机时才根据当前设置生成启动项，刚打开 PXE 的端口，要保存并重启一次，网卡项才会出现在启动顺序里。

## 先确认启动模式：UEFI PXE 和 Legacy PXE 不是一回事

同一块网卡，在 UEFI 和 Legacy 两种模式下走的是两套完全不同的引导代码，BIOS 里的开关不同，PXE 服务器要下发的文件也不同：

| 项目 | UEFI 模式 | Legacy 模式 |
| --- | --- | --- |
| BIOS 里的开关 | Network Stack、PXE Device、UEFI PXE Boot Policy | 网卡 Option ROM、CSM 里的 Network 选项 |
| 启动项名称举例 | `UEFI: PXE IPv4 Intel(R) Ethernet ...` | `IBA GE Slot ...`、 `NIC in Slot ...`，因网卡固件而异 |
| DHCP 请求里的客户端架构（选项 93） | x64 UEFI 多为 7，部分固件为 9 | 0 |
| 要下发的引导文件 | `ipxe.efi` ，或签名的 `shimx64.efi` 加 `grubx64.efi` | `pxelinux.0` 、 `undionly.kpxe` |
| Secure Boot | 生效，引导程序必须带固件信任的签名 | 不适用 |

装机服务器按选项 93 区分两类机器，下发对应的文件；BIOS 模式和服务器端配置对不上，就会出现“拿到了地址却加载不了引导文件”。新平台正在淘汰 Legacy：HPE ProLiant Gen11 已不再提供 Legacy 启动模式，其他品牌的新平台也在收缩 Legacy 支持（以机型文档为准），新上架的机器建议统一用 UEFI PXE。

## 各品牌 BIOS 里的菜单位置

开机按键先记住这张表，具体以开机画面的提示为准：

| 品牌 | 进 BIOS 设置 | 启动菜单（一次性） | 直接网络启动 |
| --- | --- | --- | --- |
| Dell PowerEdge | F2 | F11 | F12 |
| HPE ProLiant Gen9 及以后 | F9 | F11 | F12 |
| 联想 ThinkSystem | F1 | F12 | 在 F12 菜单中选网卡 |
| 超微 | Del | F11 | F12 |
| 浪潮、华为等 | 多数为 Del | 多数为 F11 | 多数为 F12 |

### Dell PowerEdge（14 代及以后，UEFI 模式）

1. 开机按 F2，进入 System BIOS → Network Settings。
2. 把 PXE Device1 设为 Enabled，进入 PXE Device1 Settings：Interface 选接装机网的端口（例如 NIC Integrated 1 Port 1 Partition 1），Protocol 选 IPv4 或 IPv6；装机网需要带标签 VLAN 时，在这里打开 VLAN 并填写 VLAN ID。
3. 最多可以配 4 个 PXE Device，用不到的保持 Disabled。
4. 回到 System BIOS → Boot Settings → UEFI Boot Settings，在 UEFI Boot Sequence 里调整 PXE Device 1 的位置。
5. Back → Finish，保存并重启。

插在扩展槽上的网卡不出现时，检查 System BIOS → Integrated Devices → Slot Disablement，对应槽位不能是 Boot Driver Disabled。13 代及更早的机器如果在 BIOS 启动模式下，要在 Device Settings 里进入对应网卡端口的 NIC Configuration，把 Legacy Boot Protocol 设为 PXE，再到 BIOS Boot Settings 的 Boot Sequence 里调整顺序。

### HPE ProLiant（Gen9、Gen10）

1. 开机按 F9，进入 System Utilities → System Configuration → BIOS/Platform Configuration (RBSU) → Network Options → Network Boot Options。
2. 找到接装机网的端口（Embedded LOM 1 Port 1、Embedded FlexibleLOM 或 PCIe Slot 网卡，名称随机型而异），设为 Network Boot。
3. 在同一个 Network Boot Options 页里找到 UEFI PXE Boot Policy，按装机网选择只用 IPv4、只用 IPv6，或两者的先后顺序（可选项随代际和固件版本不同）。
4. 按 F10 保存并重启一次，新的网络启动项生成后，到 Boot Options 下的 UEFI Boot Order 调整顺序。

### 联想 ThinkSystem

1. 开机按 F1，选择 UEFI Setup → System Settings → Network。
2. 在 Network Stack Settings 里打开 Network Stack，按需打开 IPv4 PXE Support 或 IPv6 PXE Support。
3. 部分机型在 Network Boot Settings 里还能按端口设置网络引导，确认接装机网的端口没有被关闭。
4. 在 Boot Manager → Change Boot Order 里调整网卡项的顺序，保存退出。

菜单名称随机型和固件版本略有差异，以联想对应机型的 UEFI 设置说明为准。

### 超微、浪潮等 AMI BIOS 机型

1. 开机按 Del，进入 Advanced → Network Stack Configuration：Network Stack 设为 Enabled，Ipv4 PXE Support 设为 Enabled，不用 IPv6 就把 Ipv6 PXE Support 关掉。同一页一般还有 PXE boot wait time 和 Media detect count 两项，网口链路起得慢时调大后者。
2. 需要 Legacy PXE 时，超微还要在 Advanced → PCIe/PCI/PnP Configuration 里把 Onboard LAN Option ROM Type 设为 Legacy、对应端口的 Onboard LAN Option ROM 设为 PXE；其他 AMI 机型看 CSM Configuration 里 Network 一项是加载 UEFI 还是 Legacy 驱动。
3. 在 Boot 页把 Boot mode select 设为 UEFI（或按需选 Legacy、Dual），在启动项优先级里把 UEFI Network 一类条目调到需要的位置。
4. Save & Exit。

华为、超聚变机型的 BIOS 菜单结构与通用 AMI 界面不同，PXE 开关在 Advanced 下的网卡或 PXE 配置项里按端口设置，以机型的 BIOS 参数说明为准。

## 只从网络启动一次：F12、启动菜单与 BMC

装机通常只需要从网络启动一次，装完回到硬盘。所以更稳妥的做法是：BIOS 的永久启动顺序保持硬盘在前，需要装机时再临时指定网络启动。临时指定有三种办法：

- **人在现场或在远程控制台里** ：开机时按 F12 直接网络启动（Dell、HPE、超微），或按 F11、F12 进启动菜单选网卡项。前提仍然是上一节的网络引导已经打开，否则菜单里没有网卡可选。
- **通过 BMC** ：在 BMC 网页的启动设置里选“下次从 PXE 启动”，或执行 `ipmitool chassis bootdev pxe options=efiboot` ，然后重启，只对下一次开机生效。
- **在还能登录的 Linux 里** ： `efibootmgr` 列出启动项编号，再用 `efibootmgr --bootnext <编号>` 指定下一次从网卡启动。

如果反过来把网卡永久排在硬盘前面，每次重启都会先去请求 PXE；装机服务器上一旦有任务没清理，机器就可能被重装。

## 批量设置：用 racadm 或 Redfish 改 BIOS 属性

几十台同型号机器逐台进 BIOS 太慢，可以通过 BMC 修改 BIOS 属性，下次重启时生效。Dell 的属性名比较固定：

```bash
# 查看当前网络引导相关设置
racadm get BIOS.NetworkSettings
# 启用 PXE Device1，绑定到板载 1 号口，使用 IPv4
racadm set BIOS.NetworkSettings.PxeDev1EnDis Enabled
racadm set BIOS.PxeDev1Settings.PxeDev1Interface NIC.Integrated.1-1-1
racadm set BIOS.PxeDev1Settings.PxeDev1Protocol IPv4
# 创建 BIOS 配置任务，重启后写入
racadm jobqueue create BIOS.Setup.1-1
racadm serveraction powercycle
```

其他品牌的 BIOS 属性名各不相同，先通过 Redfish 把属性列出来，找到与 PXE、Network Stack 相关的名称：

```bash
# Dell 的系统 ID 为 System.Embedded.1，HPE 等多为 1，先 GET /redfish/v1/Systems/ 确认
curl -sk -u admin:'密码' https://10.20.1.11/redfish/v1/Systems/System.Embedded.1/Bios \
  | python3 -m json.tool | grep -i -E 'pxe|network'
```

修改时向同一资源下的 `Bios/Settings` 发送 PATCH，属性写在 `Attributes` 里，重启后生效；是否需要额外创建任务、属性取值的写法，以各厂商的 Redfish 文档为准。改之前先在一台机器上验证，确认启动菜单里出现了预期的网卡项，再推到整批。

## 启动菜单里没有网卡，或者网卡一闪而过

| 现象 | 常见原因 | 处理 |
| --- | --- | --- |
| 启动菜单和启动顺序里都没有网卡项 | 网络引导没开；开在别的端口上；刚改完还没重启过 | 按前文打开对应端口，保存后重启一次再看 |
| 只看到 Legacy 网卡项，或只看到 UEFI 网卡项 | 启动模式和打开的开关不匹配 | UEFI 模式开 Network Stack，Legacy 模式开网卡 Option ROM |
| 扩展槽网卡始终不出现 | 槽位的引导驱动被禁用，或网卡自身的引导功能关闭 | 检查槽位设置；Legacy 模式下 Intel、Broadcom 网卡可在自检时按 Ctrl+S 进入引导代理设置，UEFI 模式下在 BIOS 的 Device Settings 里 |
| 网卡项出现，但很快提示没有收到 DHCP 应答并跳过 | 链路刚起来，交换机端口还在生成树收敛，传统 STP 要约 30 秒才开始转发 | 把接服务器的端口设为边缘端口：华为 `stp edged-port enable` ，思科 `spanning-tree portfast` ；链路起得慢的机型调大 Media detect count |
| 服务器两个口在交换机上做了 LACP 聚合 | PXE 阶段网卡不会协商 LACP，聚合口不转发 | 交换机侧开启 LACP 回退（各厂商叫法不同），或装机时临时改为单口 |
| 网线插在 2 号口，PXE 开在 1 号口 | 端口对不上 | 核对 PXE 设置里绑定的端口，对照交换机 MAC 表确认线接在哪 |
| 装机网要求带 VLAN 标签 | 固件发出的 DHCP 请求默认不带标签 | 交换机端口设为装机 VLAN 的 access 口，或在 BIOS 的 PXE 设置里配置 VLAN（Dell 等支持） |
| 已下载引导文件，随后报安全违规或拒绝访问 | Secure Boot 拒绝未签名的引导程序 | 改用发行版提供的签名 shim 加 GRUB，或在装机期间关闭 Secure Boot |

拿到了地址之后出现 PXE-E 开头的报错，问题通常已经不在 BIOS，而在 DHCP、TFTP 和引导文件的配置上，要到装机服务器一侧排查。

## 在管理系统里怎么落地

BIOS 里的网络引导是一次性的上架工作，之后每次装机只需要“下次从 PXE 启动并重启”。在 Toplink DCIM 里，这一步放在 [IPMI 远程管理](https://www.dcim.co/dcim/ipmi/) ：启动项可以指定为 PXE、在线 ISO 或硬盘，只对下一次开机生效；整批机器要重装时，统一设为 PXE 启动后交给任务队列执行，后台能看到每台的进度；VNC 控制台走 BMC 通道，能看到网卡获取地址、加载引导文件的全过程，卡在哪一步一眼可知。装机网和业务网分在不同 VLAN 时，服务器接入端口的 VLAN 可以在 [交换机管理](https://www.dcim.co/dcim/switch/) 里直接修改，不必逐台登录交换机。

## 常见问题

PXE 启动一直停在 Start PXE over IPv6 怎么办？

固件会按设定的顺序先后尝试 IPv4 和 IPv6，没有部署的那个协议要等超时才轮到下一个，看起来像卡住。装机网只用 IPv4 时，把 PXE 协议改成 IPv4，或者关掉 IPv6 PXE Support。

网线插在 BMC 管理口上能 PXE 启动吗？

不能。PXE 由主机的业务网卡发起，BMC 专用管理口只属于 BMC，主机无法从它启动。共享口模式下 BMC 借用的那块板载网卡本身可以 PXE，但主机和 BMC 在这个口上用的是两个不同的 MAC 和地址。

PXE、iPXE 和 HTTP Boot 是什么关系？

PXE 是网卡固件里的标准流程，用 DHCP 拿地址、用 TFTP 下载引导文件；iPXE 是开源的网络引导程序，常由 PXE 先加载，再用 HTTP 等协议拉取内核和镜像；UEFI HTTP Boot 是 UEFI 规范里直接从 HTTP 地址启动的方式，BIOS 中通常有单独的 HTTP Support 开关。

怎么确认 PXE 请求是从哪个网口发出的？

看装机网 DHCP 服务器的日志，里面记录了发请求的客户端 MAC；再到 BMC 网页的网卡清单或 BIOS 的网卡信息里对照这个 MAC 属于哪个端口。日志里一条请求都没有，说明请求没有到达装机网，先查线接在哪个口、交换机端口在哪个 VLAN。