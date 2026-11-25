你好！我是你的 Linux 命令行讲师。`ethtool` 是网卡的"体检仪 + 控制面板"。网线插上却在协商到百兆、网卡时通时断、要确认光模块收发光功率——这些全是 `ethtool` 的活。它是 IDC 网络排障的必备命令。

---

### A. 一句话定义和适用场景

**`ethtool` 是一个用于查询和配置网卡（以太网设备）驱动与硬件参数的命令，可查看/设置速率、双工模式、链路状态、自协商，并读取收发统计。**

**适用场景**：业务反馈网速慢、交换机端口灯异常、怀疑网线/光模块问题、要确认网卡是否跑满negotiated 速率时，第一个敲 `ethtool <网卡名>`。它主要管**网卡硬件层**，和管 IP/路由的 `[[ip命令]]` 分工不同。

---

### B. 最常用的 5 个命令形式

#### 1. `ethtool ens33` 【只读/安全】
*   **作用**：查看网卡的链路状态、速率、双工、自协商等核心信息。
*   **参数解释**：`eth0` 换成实际网卡名（用 `[[ip命令]]` 的 `ip link` 先查）。最常用的形式。

```shell
[root@localhost ~]# ethtool ens33
# 命令：查看网卡 ens33 的驱动、链路、速率、双工、自协商、唤醒等设置
Settings for ens33:
# 以下是网卡 ens33 的设置信息
        Supported ports: [ TP ]
        # 网卡支持的端口类型：[ TP ]
        # TP = Twisted Pair，双绞线（常见以太网网线）
        Supported link modes:   10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
        # 网卡硬件支持的链路模式：
        # 10baseT/Half：10Mbps，双绞线，半双工
        # 10baseT/Full：10Mbps，双绞线，全双工
        # 100baseT/Half：100Mbps，双绞线，半双工
        # 100baseT/Full：100Mbps，双绞线，全双工
        # 1000baseT/Full：1000Mbps（1Gbps），双绞线，全双工
        Supported pause frame use: No
        # 是否支持暂停帧（PAUSE frame）流控：不支持
        Supports auto-negotiation: Yes
        # 是否支持自动协商：支持
        # 自动协商用于和链路对端协商速率、双工模式等
        Supported FEC modes: Not reported
        # 支持的 FEC（前向纠错）模式：未报告
        # FEC 常用于高速以太网中降低误码率
        Advertised link modes:  10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
        # 网卡当前向链路对端“通告”的链路模式：
        # 即本端告诉对端自己支持并愿意协商哪些速率/双工模式
        Advertised pause frame use: No
        # 当前通告的暂停帧流控能力：不支持/未启用
        Advertised auto-negotiation: Yes
        # 当前通告的自动协商能力：开启
        # 表示本端会参与自动协商
        Advertised FEC modes: Not reported
        # 当前通告的 FEC 模式：未报告
        Speed: 1000Mb/s
        # 当前实际链路速率：1000Mb/s，即 1Gbps
        Duplex: Full
        # 当前双工模式：Full，全双工
        # 表示可以同时发送和接收数据
        Port: Twisted Pair
        # 当前使用的端口类型：Twisted Pair，双绞线
        PHYAD: 0
        # PHY 地址：0
        # PHY 是物理层收发器芯片，PHYAD 是其地址标识
        Transceiver: internal
        # 收发器类型：internal，内置收发器
        Auto-negotiation: on
        # 自动协商状态：开启
        MDI-X: off (auto)
        # MDI-X（介质相关接口交叉）状态：off，自动模式
        # 用于处理直通线和交叉线的自动适配；off(auto) 表示当前关闭，但处于自动模式
        Supports Wake-on: d
        # 支持的 Wake-on-LAN 唤醒方式：d
        # d 通常表示 disable，即支持“禁用唤醒”这一选项
        Wake-on: d
        # 当前 Wake-on-LAN 状态：d
        # d = disable，表示当前未启用网络唤醒
        Current message level: 0x00000007 (7)
                               drv probe link
        # 当前 ethtool 消息级别：0x00000007，即十进制 7
        # 对应的消息类别为：
        # drv：驱动相关消息
        # probe：设备探测相关消息
        # link：链路状态相关消息
        Link detected: yes
        # 是否检测到链路：yes
        # 表示网卡与对端/交换机之间的物理链路已连接
```

#### 2. `ethtool -i eth0` 【只读/安全】
*   **作用**：查看网卡驱动名、驱动版本、固件版本（firmware-version）。
*   **参数解释**：`-i` = driver info。排查兼容性、确认是否需要升级驱动/固件时用。

#### 3. `ethtool -S eth0` 【只读/安全】
*   **作用**：查看网卡的收发统计计数器（丢包、错误包等）。
*   **参数解释**：`-S` = statistics。重点看 `rx_dropped`、`tx_errors`、`rx_crc_errors`——数值持续增长说明有链路问题。

#### 4. `ethtool -m eth0`（光口）【只读/安全】
*   **作用**：读取光模块的诊断信息（DDM），包括收发光功率（dBm）、温度、电压。
*   **参数解释**：`-m` = module。仅光口网卡支持，是排查光纤链路的利器，呼应 `[[线缆#光模块]]`。

#### 5. `ethtool -p eth0` 【会改变状态/谨慎】
*   **作用**：让网卡的指示灯闪烁，用于在机房里**物理定位这块网卡对应哪个网口**。
*   **参数解释**：`-p` = identify。按 `Ctrl+C` 停止闪烁。这是"定位"用途，不改配置，但会让端口灯狂闪，操作前告知周围同事。

> ⚠️ **危险操作预警**：`ethtool -s eth0 speed 1000 duplex full` 这类带 `-s`（settings）强制改速率/双工的命令**要格外小心**。生产环境改错会导致链路中断!除非明确知道对端交换机端口也配成一致，否则**不要强制关闭自协商**。

---

### C. "只读安全"示例：读懂输出

执行 `ethtool ens1f0`：

```text
Settings for ens1f0:
    Supported ports: [ FIBRE ]
    Supported link modes:   10000baseT/Full
    Speed: 10000Mb/s
    Duplex: Full
    Port: FIBRE
    Transceiver: external
    Auto-negotiation: on
    Link detected: yes
```

**字段解析**：
*   **`Speed: 10000Mb/s`**：当前协商速率 10G。**如果这里显示 1000Mb/s 而网卡是万兆卡，说明降速了**——检查网线类别、光模块、交换机端口配置。
*   **`Duplex: Full`**：全双工。若显示 `Half`（半双工）基本是故障，现代网络不应出现。
*   **`Port: FIBRE`**：光口（还有 `TP` 双绞线电口、`DA` 直连线）。光口对应光模块，电口对应 `[[线缆#双绞线]]`。
*   **`Auto-negotiation: on`**：自协商开启，正常应保持 on。
*   **`Link detected: yes`**：链路是否建立。**`no` 就是物理层不通**——先查网线/光纤、光模块、对端端口，而不是查 IP。

**💡 读光功率**：`ethtool -m` 输出里看 `Rx Power`（接收光功率），正常一般在 `-3 ~ -20 dBm`。若显示 `-40 dBm` 或更低，说明光纤断了/对端没发光/光口脏——对应 `[[线缆#线缆选型速查]]` 里的排障速记，用 `[[光功率计使用教程：测量方法与技巧|光功率计]]`、`[[红光笔]]`、`[[光纤清洁笔]]` 处理。

---

### D. IDC 运维高危大坑（改配置一时爽，断网火葬场）

**场景：强行改速率导致链路 down**
新手看到万兆口只跑千兆，直接 `ethtool -s eth0 speed 10000 duplex full autoneg off` 强制——如果对端交换机端口没同步改、或线材根本不支持，端口立刻 down，业务中断。

**正确排查顺序**：
1. 先只读确认：`ethtool -S` 看有没有 CRC 错误、`ethtool -m` 看光功率。
2. 查物理层：网线是否 Cat6a（万兆需要，见 `[[线缆#双绞线]]`）、光模块是否匹配单多模、**光纤清洁**（很多"莫名其妙"的降速是光口脏了）。
3. 查对端：交换机端口是否被限速、是否配置了错误的 duplex。
4. 最后才考虑改配置，且**必须在变更窗口、做好回退方案**，遵循 `[[SOP]]` 和 `[[运维红线]]`。

---

### E. 常见误区与排错思路

1.  **误区：网口不通就改 IP**
    *   **纠正**：先 `ethtool <网卡>` 看 `Link detected`。如果是 `no`，这是**物理层**问题（线、模块、端口），改 IP 完全没用。
2.  **误区：`-S` 统计里有点错误就慌**
    *   **纠正**：要看**是否持续增长**。开机瞬间的零星错误可能是正常协商过程。用 `watch -n1 'ethtool -S eth0 | grep error'` 观察增量。
3.  **误区：光口读不到 `-m` 信息就以为模块坏了**
    *   **纠正**：有些网卡/模块不支持 DDM，或驱动没开启。先 `ethtool -i` 看驱动支持情况，再用 `[[光模块测试仪]]` 等专用工具测。

---

### F. 小练习（自己动手试试）

1.  **（基础）** 用 `ip link` 找到你的网卡名，执行 `ethtool <网卡名>`，记录 Speed、Duplex、Link detected 三项。
2.  **（进阶）** 执行 `ethtool -i <网卡名>`，看看驱动名和版本，思考驱动版本对排障的意义。
3.  **（排错）** 执行 `ethtool -S <网卡名> | grep -i -E 'error|drop'`，观察有没有非零计数，想想这些计数增长意味着什么。

---

### G. 是否继续深入？

`ethtool` 管网卡硬件层，接下来你可以：
*   用 `[[ip命令]]` 看/配 IP 地址和路由；
*   用 `[[nmcli]]` 管理网络连接；
*   或深挖"网卡降速/丢包"的完整排查流程。告诉我！
