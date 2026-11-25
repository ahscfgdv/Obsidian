下面是一份整合了**原理、交换机配置**以及**两种服务器配置方法（传统 network 服务 vs NetworkManager/nmcli）**的完整文档。

在 CentOS 7 中配置 Bond，**这两种方法是互斥的**。请根据你的服务器现状（是否开启了 NetworkManager）选择其中一种，**切勿混用**。

---

#### 前置说明：二选一原则

-   **方法一：传统 network 服务**。适合习惯修改配置文件、追求直观稳定的生产环境。**必须关闭并禁用 NetworkManager**。
-   **方法二：NetworkManager (nmcli)**。适合习惯命令行操作、自动化脚本或不想关闭 NetworkManager 的环境。**无需手写配置文件**。

配置假设：物理网卡为 `ens33`、`ens34`；IP 为 `192.168.1.100/24`；网关为 `192.168.1.1`。

---

#### 核心原理与交换机侧配置速览

无论用哪种服务器配置方法，交换机的配合规则是固定的：

| 模式 | 核心原理 | 交换机侧配置要求 | 带宽与容错 |
| :--- | :--- | :--- | :--- |
| **bond0**<br>(balance-rr) | **轮询**发送数据包，理论带宽叠加。但存在数据包乱序风险。 | **需配置**静态链路聚合（无需 LACP 协议）。或两端分别插在不同交换机。 | 带宽叠加，有容错。 |
| **bond1**<br>(active-backup) | **主备**模式。同一时刻只有一块网卡工作，另一块待命。 | **无需任何特殊配置**。交换机会看到同一个 MAC 地址在切换。 | 带宽不叠加，高容错。 |
| **bond4**<br>(802.3ad) | 基于 **LACP 协议**动态协商，实现智能负载均衡与容错。 | **必须开启** LACP 动态协商，且两端速率双工一致。 | 带宽叠加，高容错。 |

**交换机配置示例（以 bond0 / bond4 为例）**：

-   **华为交换机 (LACP 模式 - 对应 bond4)**：
    ```text
    interface Eth-Trunk 1
     mode lacp-static
    interface GigabitEthernet 0/0/1
     eth-trunk 1
    interface GigabitEthernet 0/0/2
     eth-trunk 1
    ```
-   **Cisco 交换机 (LACP 模式 - 对应 bond4)**：
    ```text
    interface range GigabitEthernet0/1 - 2
     channel-group 1 mode active
    ```
-   *注：bond0 通常对应 Cisco 的 `mode on` 或华为的 `mode manual load-balance`。*

---

#### 方法一：传统 network 服务（修改配置文件）

**1. 前置操作：关闭 NetworkManager**
```bash
systemctl stop NetworkManager
systemctl disable NetworkManager
```

**2. bond0 配置（balance-rr）**
创建 `/etc/sysconfig/network-scripts/ifcfg-bond0`：
```ini
DEVICECE=bond0
NAME=bond0
TYPE=Bond
BONDING_MASTER=yes
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.184.137
PREFIX=24
GATEWAY=192.168.184.2
# mode=0 表示轮询；miimon=100 表示每100ms检测一次链路
BONDING_OPTS="mode=0 miimon=100"
NM_CONTROLLED=no
```
修改 `/etc/sysconfig/network-scripts/ifcfg-ens33`（ens34 同理）：
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

**3. bond1 配置（active-backup）**
创建 `/etc/sysconfig/network-scripts/ifcfg-bond1`：
```ini
DEVICE=bond1
NAME=bond1
TYPE=Bond
BONDING_MASTER=yes
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.100
PREFIX=24
GATEWAY=192.168.1.1
# mode=1 表示主备；primary=ens33 表示首选 ens33 为主
BONDING_OPTS="mode=1 miimon=100 primary=ens33"
NM_CONTROLLED=no
```
从属网卡 `ifcfg-ens33` 中的 `MASTER=bond1`。

**4. bond4 配置（802.3ad）**
创建 `/etc/sysconfig/network-scripts/ifcfg-bond4`：
```ini
DEVICE=bond4
NAME=bond4
TYPE=Bond
BONDING_MASTER=yes
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.100
PREFIX=24
GATEWAY=192.168.1.1
# mode=4 表示LACP；lacp_rate=fast 表示快速发送LACP报文
BONDING_OPTS="mode=4 miimon=100 lacp_rate=fast xmit_hash_policy=layer3+4"
NM_CONTROLLED=no
```
从属网卡 `ifcfg-ens33` 中的 `MASTER=bond4`。

**5. 生效与验证**
```bash
systemctl restart network
cat /proc/net/bonding/bond0  # 查看状态（替换为对应的 bond 名称）
```

---

#### 方法二：NetworkManager (nmcli) 命令行

**1. 前置操作：确保 NetworkManager 运行**
```bash
systemctl start NetworkManager
systemctl enable NetworkManager
```

**2. bond0 配置**
```bash
# 创建 bond0 接口
nmcli connection add type bond con-name bond0 ifname bond0 bond.options "mode=balance-rr,miimon=100"
# 将网卡加入 bond0
nmcli connection add type ethernet slave-type bond con-name bond0-port1 ifname ens33 master bond0
nmcli connection add type ethernet slave-type bond con-name bond0-port2 ifname ens34 master bond0
# 配置 IP
nmcli connection modify bond0 ipv4.addresses '192.168.1.100/24' ipv4.gateway '192.168.1.1' ipv4.method manual
# 启动
nmcli connection up bond0
```

**3. bond1 配置**
```bash
nmcli connection add type bond con-name bond1 ifname bond1 bond.options "mode=active-backup,miimon=100,primary=ens33"
nmcli connection add type ethernet slave-type bond con-name bond1-port1 ifname ens33 master bond1
nmcli connection add type ethernet slave-type bond con-name bond1-port2 ifname ens34 master bond1
nmcli connection modify bond1 ipv4.addresses '192.168.1.100/24' ipv4.gateway '192.168.1.1' ipv4.method manual
nmcli connection up bond1
```

**4. bond4 配置**
```bash
nmcli connection add type bond con-name bond4 ifname bond4 bond.options "mode=802.3ad,miimon=100,lacp_rate=fast,xmit_hash_policy=layer3+4"
nmcli connection add type ethernet slave-type bond con-name bond4-port1 ifname ens33 master bond4
nmcli connection add type ethernet slave-type bond con-name bond4-port2 ifname ens34 master bond4
nmcli connection modify bond4 ipv4.addresses '192.168.1.100/24' ipv4.gateway '192.168.1.1' ipv4.method manual
nmcli connection up bond4
```

**5. 生效与验证**
```bash
nmcli connection show  # 查看连接状态
cat /proc/net/bonding/bond4  # 验证内核层状态
```

---

#### ⚠️ 常见排错与避坑

1.  **“Device or resource busy” 报错**：通常是因为你在 NetworkManager 运行的情况下，尝试用 `systemctl restart network` 重启网络。**务必确认你只使用了其中一种方法。**
2.  **bond4 和 bond0不通**：99% 的原因是交换机侧没开 LACP，或者两端 `lacp_rate` 不一致。可以查看 `cat /proc/net/bonding/bond4` 里的 `LACP rate` 和 `Aggregator ID`。
3.  **虚拟化环境坑点**：在 VMware 等虚拟机中做实验，**千万别用 bond0**。虚拟交换机不支持静态链路聚合，bond0 会导致网络频繁中断。虚拟机实验请优先用 **bond1（免配置）** 或 **bond4（需在虚拟交换机开启 LACP）**。
4. 配置了bond0之后因为又配置了bond1，没有删除bond0的的配置文件或者两个配置文件都是启动的状态导致网络无法正常使用

