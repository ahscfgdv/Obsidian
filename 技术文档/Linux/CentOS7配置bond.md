下面按 **bond0、bond1、bond4** 分别给出原理、CentOS 7 配置以及对应的交换机配置要点。配置中统一假设物理网卡为 **ens33、ens34**，请按实际网卡名替换。

---

#### 原理速览

**bond0（balance-rr）**：轮询发送，每个数据包依次从不同物理网卡发出，理论上可叠加带宽，并提供容错。但同一连接的数据包可能走不同链路，存在**乱序风险**，可能导致吞吐量不升反降。需要交换机配合做**静态链路聚合**（或两端接入不同交换机）。

**bond1（active-backup）**：同一时刻只有一块网卡工作，另一块待命；主链路故障时备链路自动接管。**交换机无需任何特殊配置**，即插即用。带宽不叠加，资源利用率仅为 1/N，但可靠性最高、配置最简单。

**bond4（802.3ad / LACP）**：基于 IEEE 802.3ad 标准的动态链路聚合，通过 **LACP 协议**与交换机协商，将多条物理链路捆绑为一条逻辑链路，实现负载均衡与容错。**交换机必须开启 LACP**，两端协商成功后才能工作，否则链路不通。这是企业级环境中最标准的聚合方式。

---

#### CentOS 7 通用前置操作

三种模式共用以下前置步骤：

```bash
# 1. 备份现有网络配置
cp -a /etc/sysconfig/network-scripts /root/network-scripts.bak

# 2. 关闭并禁用 NetworkManager（避免与 network 服务冲突）
systemctl stop NetworkManager
systemctl disable NetworkManager

# 3. 确认 bonding 模块可用（有 BONDING_OPTS 时通常自动加载）
modprobe bonding
lsmod | grep bonding
```


#### bond0 配置（balance-rr）

**原理**：轮询，需要交换机静态聚合。

**创建 bond0 主接口**（`/etc/sysconfig/network-scripts/ifcfg-bond0`）：

```ini
DEVICE=bond0
NAME=bond0
TYPE=Bond
BONDING_MASTER=yes
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
BONDING_OPTS="mode=0 miimon=100"
NM_CONTROLLED=no
```

**配置从属网卡**（`ifcfg-ens33`、`ifcfg-ens34`，两者相同）：

```ini
DEVICE=ens33
NAME=ens33
TYPE=Ethernet
BOOTPROTO=none
ONBOOT=yes
MASTER=bond0
SLAVE=yes
NM_CONTROLLED=no
```

**交换机侧**：bond0 通常需要交换机配置**静态链路聚合**（如 Cisco 的 `channel-group mode on`，华为的 `Eth-Trunk` 手工模式）。不需要 LACP 协议，但交换机上必须把对应端口捆绑为一个逻辑口。如果两端接入的是**不同的交换机**（无聚合），也可以工作，此时交换机不需要聚合配置。


#### bond1 配置（active-backup）

**原理**：主备，交换机无需任何配置。

**创建 bond1 主接口**（`ifcfg-bond1`）：

```ini
DEVICE=bond1
NAME=bond1
TYPE=Bond
BONDING_MASTER=yes
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
BONDING_OPTS="mode=1 miimon=100 primary=ens33"
NM_CONTROLLED=no
```

`primary=ens33` 用于**指定 ens33 为首选活动网卡**，不写则由内核自行选择。从属网卡配置与 bond0 相同，只需把 `MASTER=` 改为 `bond1`。

**交换机侧**：**无需任何特殊配置**。两块网卡可以接入同一台交换机的普通 access/trunk 口，也可以接入不同交换机。交换机看到的是 bond1 的**同一个 MAC 地址**，但同一时刻只有一个端口有流量，不存在 MAC 地址漂移问题。


#### bond4 配置（802.3ad / LACP）

**原理**：动态 LACP 协商，交换机必须开启 LACP，两端模式匹配后才能聚合。

**创建 bond4 主接口**（`ifcfg-bond4`）：

```ini
DEVICE=bond4
NAME=bond4
TYPE=Bond
BONDING_MASTER=yes
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
BONDING_OPTS="mode=4 miimon=100 lacp_rate=fast xmit_hash_policy=layer3+4"
NM_CONTROLLED=no
```

关键参数：
- `mode=4`：802.3ad 动态聚合。
- `lacp_rate=fast`：LACP 报文每秒发送一次，加快故障检测；交换机侧建议匹配（部分平台对应 `lacp timeout short`）。
- `xmit_hash_policy=layer3+4`：基于 IP 和端口做哈希，比默认二层哈希更均匀。

从属网卡配置同 bond0/bond1，把 `MASTER=bond4` 即可。

**交换机侧（必须配置 LACP）**：

**华为**：

```text
# 创建 Eth-Trunk 并设为 LACP 模式
interface Eth-Trunk 1
 mode lacp-static
 quit

# 将两个物理口加入 Eth-Trunk
interface GigabitEthernet 0/0/1
 eth-trunk 1
 quit
interface GigabitEthernet 0/0/2
 eth-trunk 1
 quit
```

华为的 `mode lacp-static` 表示静态 LACP 模式，需与服务器侧主动 LACP 配合。若交换机侧完全不配，bond4 协商会失败，网络不通。

**Cisco**：

```text
interface range GigabitEthernet0/1 - 2
 channel-group 1 mode active
 no shutdown
 exit

interface Port-channel 1
 switchport mode trunk    # 或 access，按实际业务 VLAN 配置
 no shutdown
```

`mode active` 表示主动发送 LACP 报文，与服务器侧的 `mode=4` 对接。


#### 验证与排错

**通用验证**：

```bash
cat /proc/net/bonding/bondX
ip addr show bondX
```

重点看 `Bonding Mode` 是否与预期一致、`MII Status` 是否为 up、`Slave Interface` 是否都处于 up。

**bond4 额外检查**：

```bash
cat /proc/net/bonding/bond4 | grep -E "LACP|Aggregator|Actor|Partner"
```

如果 `Aggregator ID` 为 `N/A` 或 `LACP rate` 与交换机不匹配，说明协商未成功。常见原因：交换机侧未开 LACP、两端速率/双工不一致、`lacp_rate` 不匹配。

**故障切换测试（bond1/bond4）** ：拔掉一块网卡的网线（或在虚拟化平台中断开该网卡连接），观察 `cat /proc/net/bonding/bondX` 中活动网卡是否自动切换，以及业务是否短暂中断。bond1 切换通常有短暂丢包，bond4 在 LACP 正常时切换更平滑。


#### 选型速查

| 模式 | 交换机配置要求 | 带宽叠加 | 配置复杂度 | 典型场景 |
| :--- | :--- | :--- | :--- | :--- |
| **bond0** | 需静态聚合（或分接不同交换机） | 是（但有乱序风险） | 中 | 特定实验/简单场景，生产慎用 |
| **bond1** | **无需** | 否 | 低 | 管理口、对带宽无要求的冗余链路 |
| **bond4** | **必须开启 LACP** | 是（标准聚合） | 高 | 数据库、存储、高吞吐生产环境 |