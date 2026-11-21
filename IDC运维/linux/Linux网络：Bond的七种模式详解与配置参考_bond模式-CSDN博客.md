> 在计算机网络中，为了提高网络的高可用性和负载均衡，通常会采用链路聚合技术。Bond（绑定）技术是Linux系统中实现链路聚合的一种手段。本文将详细介绍Bond的七种模式，并探讨与LACP（链路聚合控制协议）、链路聚合等相关技术。

### 一、Bond简介

Bond是 Linux 内核提供的一种网络驱动，它可以将多个网络接口聚合为一个逻辑接口，从而提高网络带宽、实现负载均衡和故障转移。Bond支持以下七种模式：

1. bond0：Balance-RR（轮询模式）
2. bond1：Active-Backup（主备模式）
3. bond2：Balance-XOR（平衡异或模式）
4. bond3：Broadcast（广播模式）
5. bond4：802.3ad（LACP模式）
6. bond5：Balance-TLB（自适应传输负载均衡模式）
7. bond6：Balance-ALB（自适应负载均衡模式）

| 模式编号 | 模式名称                     | 描述                               | 交换机支持要求                              | 常用性  |
| ---- | ------------------------ | -------------------------------- | ------------------------------------ | ---- |
| 0    | **Balance-RR（轮询模式）**     | 数据包依次发送到所有网络接口上，实现负载均衡。          | 需要配置静态链路聚合 mode on                   | 常用   |
| 1    | **Active-Backup（主备模式）**  | 只有一个接口处于活动状态，其他作为备份，活动接口故障时自动切换。 | 不需要交换机做配置，仅需要划分对应的vlan               | 非常常用 |
| 2    | Balance-XOR（平衡异或模式）      | 根据源MAC地址和目的MAC地址的异或值来选择发送数据的接口。  | 需要配置静态链路聚合 mode on ，同时需要设置对应的balance | 较少使用 |
| 3    | Broadcast（广播模式）          | 所有接口都发送相同的数据包，适用于广播或多播场景。        | 需要配置静态链路聚合 mode on                   | 很少使用 |
| 4    | **802.3ad（LACP模式）**      | 遵循LACP协议，通过LACP协商实现链路聚合。         | 需要配置lacp动态链路聚合 mode active           | 常用   |
| 5    | Balance-TLB（自适应传输负载均衡模式） | 根据每个接口的负载情况动态调整数据包发送。            | 不需要                                  | 较少使用 |
| 6    | Balance-ALB（自适应负载均衡模式）   | Bond5模式的扩展，同时实现发送和接收的负载均衡。       | 不需要                                  | 较少使用 |

#### 最常用的几种模式：

- **Active-Backup（主备模式）** ：因其简单和可靠性，通常用于需要高可用性的场景。
- **Balance-RR（轮询模式）** ：因其简单实现负载均衡，适用于需要提高带宽的场景。
- **802.3ad（LACP模式）** ：在需要遵循标准协议且交换机支持LACP的环境中广泛使用。

### 二、Bond七种模式详解

> 在Linux系统中配置Bond时，通常会在`/etc/sysconfig/network-scripts/ifcfg-bondX`文件中设置`BONDING_OPTS="mode=模式编号"`来指定Bond的工作模式。例如，如果要配置一个轮询模式的Bond，可以设置`BONDING_OPTS="mode=0"`。

#### 1\. Mode 0 - Balance- RR （轮询模式）（常用）

```markdown
特点：
- 轮流发送数据包，实现负载均衡
- 不需要交换机支持
- 模式编号：0
markdown1234
```

#### 2\. Mode 1 - Active-Backup（主备模式）（常用）

```markdown
特点：
- 提高网络可靠性
- 只有一个网络接口处于活动状态，其他接口作为备份
- 需要交换机支持链路聚合
- 模式编号：1
markdown12345
```

#### 3\. Mode 2 - Balance-XOR（平衡异或模式）

```markdown
特点：
- 根据源目MAC地址进行负载均衡
- 需要交换机支持链路聚合
- 模式编号：2
markdown1234
```

#### 4\. Mode 3 - Broadcast（广播模式）

```markdown
特点：
- 所有接口都发送相同的数据包
- 适用于需要广播或多播的应用场景
- 模式编号：3
markdown1234
```

#### 5\. Mode 4 - 802.3ad（LACP模式）（常用）

```markdown
特点：
- 遵循LACP协议，实现链路聚合
- 需要交换机支持LACP
- 模式编号：4
markdown1234
```

#### 6\. Mode 5 - Balance-TLB（自适应传输 负载均衡 模式）

```markdown
特点：
- 动态调整数据包发送，实现负载均衡
- 使用ARP协商来决定哪个接口用于发送数据包。
- 当需要发送数据包时，bonding驱动会检查哪个接口的负载最低，并将数据包发送到该接口。
- 如果目标主机发送了ARP回复，bonding驱动会记录这个接口用于将来的数据包发送。
- 不需要交换机支持
- 模式编号：5
markdown1234567
```

#### 7\. Mode 6 - Balance-ALB（自适应负载均衡模式）

```markdown
特点：
- 同时实现发送和接收负载均衡
- 对于发送（TX）负载均衡，Bond6使用类似于Bond5的方法，通过ARP协商来确定发送接口。
- 对于接收（RX）负载均衡，bonding驱动通过修改MAC地址哈希来分配接口，使得不同接口可以接收来自不同源的数据包。
- Bond6还支持将多播和广播流量分散到不同的接口上。
- 不需要交换机支持
- 模式编号：6
markdown1234567
```

### 三、配置案例

以下是一个在Linux系统中配置bond0、bond1和bond4的示例。请注意，具体的配置可能会根据您的Linux发行版和版本有所不同，以下示例基于Red Hat/ CentOS 发行版。

#### 1\. 配置bond0（Balance-RR模式）

创建或编辑以下网络接口 配置文件 ：  
**/etc/sysconfig/network-scripts/ifcfg-bond1**

```bash
DEVICE=bond1
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.101
NETMASK=255.255.255.0
BONDING_OPTS="mode=0 miimon=100"
bash123456
```

为每个物理网络接口创建配置文件：  
**/etc/sysconfig/network-scripts/ifcfg-eth2**

```bash
DEVICE=eth2
BOOTPROTO=none
ONBOOT=yes
MASTER=bond1
SLAVE=yes
bash12345
```

**/etc/sysconfig/network-scripts/ifcfg-eth3**

```bash
DEVICE=eth3
BOOTPROTO=none
ONBOOT=yes
MASTER=bond1
SLAVE=yes
bash12345
```

#### 2\. 配置bond1（Active-Backup模式）

首先，创建或编辑以下网络接口配置文件：  
**/etc/sysconfig/network-scripts/ifcfg-bond0**

```bash
DEVICE=bond0
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
BONDING_OPTS="mode=1 miimon=100"
# mode=1: 这是指定bonding模式为1
# bonding驱动监视链路状态的频率，bonding驱动每100毫秒检查一次所有绑定的物理网络接口的状态
bash123456789
```

接下来，为每个物理网络接口创建配置文件：  
**/etc/sysconfig/network-scripts/ifcfg-eth0**

```bash
DEVICE=eth0
BOOTPROTO=none
ONBOOT=yes
MASTER=bond0
SLAVE=yes
bash12345
```

**/etc/sysconfig/network-scripts/ifcfg-eth1**

```bash
DEVICE=eth1
BOOTPROTO=none
ONBOOT=yes
MASTER=bond0
SLAVE=yes
bash12345
```

#### 3\. 配置bond4（802.3ad LACP模式）

创建或编辑以下网络接口配置文件：  
**/etc/sysconfig/network-scripts/ifcfg-bond4**

```bash
DEVICE=bond4
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.102
NETMASK=255.255.255.0
BONDING_OPTS="mode=4 miimon=100 lacp_rate=1"
# lacp_rate用于设置链路聚合的速率。1快速模式，默认每1秒发送一次LACP数据包，0慢速模式，每30秒发送一次数据包。
bash1234567
```

为每个物理网络接口创建配置文件：  
**/etc/sysconfig/network-scripts/ifcfg-eth4**

```bash
DEVICE=eth4
BOOTPROTO=none
ONBOOT=yes
MASTER=bond4
SLAVE=yes
bash12345
```

**/etc/sysconfig/network-scripts/ifcfg-eth5**

```bash
DEVICE=eth5
BOOTPROTO=none
ONBOOT=yes
MASTER=bond4
SLAVE=yes
bash12345
```

#### 启动网络服务

完成配置文件编辑后，重启网络服务以应用更改：

```bash
systemctl restart network
bash1
```

确保你的 交换机 也配置了相应的聚合组，并且如果使用LACP模式（bond4），交换机需要开启LACP功能。  
请注意，以上配置仅作为示例，实际配置时需要根据你的网络环境和需求进行调整。

### centos8中如何用team完成链路捆绑

```shell
nmcli connection add ifname team0 con-name team0 type team config '{"runner": {"name": "loadbalance"}}'
nmcli connection add ifname ens160 con-name ens160-team type team-slave autoconnect yes master team0
nmcli connection add ifname ens224 con-name ens224-team type team-slave autoconnect yes master team0
nmcli connection modify team0 ipv4.addresses 192.168.100.90/24 ipv4.gateway 192.168.100.2 autoconnect yes ipv4.method manual ipv4.dns 192.168.100.2
nmcli connection up team0
nmcli connection up ens160-team
nmcli connection up ens224-team

shell123456789
```