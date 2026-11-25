你好！我是你的 Linux 命令行讲师。`ip` 命令是现代 Linux 网络配置的"瑞士军刀"。老一代用 `ifconfig`、`route`、`arp`，现在都被 `iproute2` 工具包的 `ip` 统一取代了。面试和实操中，查 IP、配地址、看路由、看 ARP，全靠它。

---

### A. 一句话定义和适用场景

**`ip` 是一个用于查看和管理网络接口、IP 地址、路由表、ARP/邻居表、 VLAN 等的命令，属于 `iproute2` 工具包，是 `ifconfig`/`route` 的现代替代品。**

**适用场景**：确认机器 IP、临时配 IP、加/删路由、排查网关不通、查看邻居（ARP）缓存——只要是 TCP/IP 层的网络配置和排查，都用 `ip`。注意区分：`[[ethtool]]` 管网卡硬件层（速率/链路），`ip` 管 IP/路由这一层。

---

### B. 最常用的 6 个命令形式

#### 1. `ip link show`（或 `ip link`）【只读/安全】
*   **作用**：列出所有网络接口及其状态（UP/DOWN、MAC 地址、MTU）。
*   **参数解释**：看"网卡有没有起来"。`state UP` 且 `<BROADCAST,MULTICAST,UP,LOWER_UP>` 才是正常工作。

```shell
[root@localhost ~]# ip link show
# 命令：显示网络接口的“链路层”信息
# 包括接口名、接口标志、MTU、qdisc、状态、MAC 地址、广播地址等
# 注意：它默认不显示 IP 地址；查看 IP 要用 ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
# 1:                 接口索引（ifindex）为 1
# lo:                接口名称，lo 是 loopback 回环接口
# <LOOPBACK,UP,LOWER_UP>  接口标志：
#   LOOPBACK         回环接口
#   UP               管理上已启用（administrative up）
#   LOWER_UP         底层链路/载体可用（carrier up）
# mtu 65536          最大传输单元为 65536 字节
# qdisc noqueue      排队规则为 noqueue，表示该接口不使用发送队列，回环接口通常如此
# state UNKNOWN      接口状态显示为 UNKNOWN；回环接口没有传统物理链路状态，所以常见 UNKNOWN，不代表故障
# mode DEFAULT       接口模式为默认
# group default      接口所属分组为 default
# qlen 1000          发送队列长度为 1000

    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
# link/loopback      链路层类型为 loopback（回环）
# 00:00:00:00:00:00  回环接口的链路层地址，全零
# brd 00:00:00:00:00:00  广播地址，回环接口也为全零
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
# 2:                 接口索引为 2
# ens33:             接口名称，ens33 是网卡名；在 VMware 虚拟机中常见
# <BROADCAST,MULTICAST,UP,LOWER_UP>  接口标志：
#   BROADCAST        支持广播
#   MULTICAST        支持组播/多播
#   UP               管理上已启用
#   LOWER_UP         底层链路已连接，通常表示网线/虚拟链路 carrier 正常
# mtu 1500           最大传输单元为 1500 字节，是以太网常见值
# qdisc pfifo_fast   排队规则为 pfifo_fast，一种传统的优先级 FIFO 队列
# state UP           接口状态为 UP，表示接口已启用并处于可用状态
# mode DEFAULT       接口模式为默认
# group default      接口所属分组为 default
# qlen 1000          发送队列长度为 1000
    link/ether 00:0c:29:8b:97:99 brd ff:ff:ff:ff:ff:ff
# link/ether                 链路层类型为 ether，即以太网
# 00:0c:29:8b:97:99          网卡 MAC 地址；00:0c:29 是 VMware 虚拟网卡常见前缀
# brd ff:ff:ff:ff:ff:ff      以太网广播地址，全 FF
```

#### 2. `ip addr show`（或 `ip a`）【只读/安全】
*   **作用**：查看所有网卡的 IP 地址（含 IPv4/IPv6）、子网掩码。
*   **参数解释**：**最常用的"我这台机器 IP 是多少"命令**。可简写 `ip a`。

```shell
[root@localhost ~]# ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:8b:97:99 brd ff:ff:ff:ff:ff:ff
    inet 192.168.184.137/24 brd 192.168.184.255 scope global noprefixroute ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::9188:2332:7a91:994f/64 scope link noprefixroute
       valid_lft forever preferred_lft forever

```

#### 3. `ip route show`（或 `ip r`）【只读/安全】
*   **作用**：查看路由表，尤其是默认网关。
*   **参数解释**：`default via 192.168.1.1 dev eth0` 表示默认网关是 192.168.1.1。网络不通先查这里网关对不对。

```shell
[root@localhost ~]# ip route show
default via 192.168.184.2 dev ens33 proto static metric 100
192.168.184.0/24 dev ens33 proto kernel scope link src 192.168.184.137 metric 100
```

#### 4. `ip neigh show`（或 `ip n`）【只读/安全】
*   **作用**：查看邻居表（相当于 ARP 缓存），显示 IP 与 MAC 地址的映射。
*   **参数解释**：排查"IP 通但访问异常"时看有没有 ARP 解析、是否解析到错误 MAC（ARP 欺骗）。

```shell
[root@localhost ~]# ip neigh show
192.168.184.2 dev ens33 lladdr 00:50:56:ec:4e:dd STALE
192.168.184.1 dev ens33 lladdr 00:50:56:c0:00:02 DELAY
```

#### 5. `ip addr add 192.168.1.100/24 dev eth0` 【会改配置/谨慎】
*   **作用**：给网卡临时添加一个 IP 地址。
*   **参数解释**：**临时生效，重启网络/机器后丢失**。生产环境改 IP 要走 `[[nmcli]]` 或配置文件，并遵循 `[[SOP]]` 变更流程。

#### 6. `ip link set eth0 up|down` 【会改状态/谨慎】
*   **作用**：启用或停用网卡。
*   **参数解释**：`down` 一块正在跑业务的网卡 = 断网！操作前务必确认网卡名和业务影响。

---

### C. "只读安全"示例：读懂输出

执行 `ip addr show`：

```text
2: ens1f0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 3c:ec:ef:1a:2b:3c brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.11/24 brd 10.0.0.255 scope global ens1f0
       valid_lft forever preferred_lft forever
3: ens1f1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether 3c:ec:ef:1a:2b:3d brd ff:ff:ff:ff:ff:ff
```

**字段解析**：
*   **`ens1f0` / `ens1f1`**：网卡名（可预测命名：en=以太网，s1=插槽1，f0/f1=端口0/1）。
*   **`<...UP,LOWER_UP> state UP`**：`UP` 是管理状态（管理员启用），`LOWER_UP` 是物理链路已建立（网线插着、对端活着）。**两个都有才是真通**。
*   **`link/ether 3c:ec:ef...`**：MAC 地址。
*   **`inet 10.0.0.11/24`**：IPv4 地址和掩码（/24 = 255.255.255.0）。
*   **`ens1f1` 的 `<NO-CARRIER> state DOWN`**：**没插线或对端没开机**（无载波）。这是判断"网口为什么不通"的关键线索——先看物理层，呼应 `[[ethtool]]`。

执行 `ip route`：
```text
default via 10.0.0.1 dev ens1f0 proto static metric 100
10.0.0.0/24 dev ens1f0 proto kernel scope link src 10.0.0.11
```
*   `default via 10.0.0.1`：默认网关。访问外网走它。网关错 = 出不去网。

---

### D. IDC 运维高危大坑（ip 命令改配置要命）

**场景一：远程改 IP 把自己踢下线**
通过 SSH 连着服务器，执行 `ip addr add` 加了新 IP 又删了旧 IP，或者 `ip link set eth0 down`——**SSH 瞬间断开，人直接失联**，只能去机房接显示器或用 `[[ipmitool]]` 带外管理救场。

**铁律**：远程改网络配置前，① 确认有带外管理（IPMI/iDRAC/iLO）作为后路；② 用 `nohup`/`at` 设一个"定时回退"任务，万一配置错了，几分钟后自动恢复；③ 能本地操作就本地操作。这是 `[[运维红线]]` 里"高危变更"的典型。

**场景二：临时配置以为永久生效**
`ip addr add` 加的 IP **重启就没**。生产环境要永久生效，得写网络配置文件或用 `[[nmcli]]`（NetworkManager），并确认配置持久化。

---

### E. 常见误区与排错思路

1.  **误区：还用 `ifconfig` / `route`**
    *   **纠正**：这些命令在很多新系统已默认不装。统一用 `ip addr`、`ip route`。面试说 `ip` 命令才显得与时俱进。
2.  **误区：`state UP` 就以为网络通**
    *   **纠正**：要有 `LOWER_UP`（物理链路）才算真通。只有 `UP` 没有 `LOWER_UP`，多半是没插线/对端 down/光模块问题。配合 `[[ethtool]]` 的 `Link detected` 一起看。
3.  **网络不通的标准排查链**：
    *   ① `ip link` 看网卡状态 → ② `[[ethtool]]` 看物理链路 → ③ `ip addr` 看 IP 掩码 → ④ `ip route` 看网关 → ⑤ `ip neigh` 看 ARP → ⑥ `ping` 网关 → ⑦ `ping` 外网 → 逐层定位。

---

### F. 小练习（自己动手试试）

1.  **（基础）** 执行 `ip -br addr`（简洁模式），记下你的网卡名、状态和 IP。
2.  **（进阶）** 执行 `ip route`，找出默认网关；再执行 `ping -c 4 <网关IP>` 测试到网关通不通。
3.  **（排错）** 执行 `ip neigh`，观察邻居表里有没有 `FAILED` 状态的条目，想想它可能意味着什么。

---

### G. 是否继续深入？

`ip` 管 IP/路由，接下来你可以：
*   用 `[[nmcli]]` 学习持久化的网络连接管理（生产环境改配置更稳妥）；
*   用 `[[ethtool]]` 深入网卡硬件层排障；
*   或整理一份"网络不通七步排查法"速查卡。告诉我！
