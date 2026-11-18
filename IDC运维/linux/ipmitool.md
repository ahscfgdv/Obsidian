你好！我是你的 Linux 命令行讲师。`ipmitool` 是带外管理（BMC/iDRAC/iLO）的命令行瑞士军刀。服务器死机、系统起不来、SSH 连不上时，只要电源还通、网线还连着管理口，你就能通过它远程开机、看日志、读传感器——这是 IDC 运维"人不在机房也能救场"的看家本领。

---

### A. 一句话定义和适用场景

**`ipmitool` 是一个通过 IPMI（智能平台管理接口）协议与服务器 BMC（基板管理控制器）通信的命令，可远程管理电源、读取传感器、查看系统事件日志（SEL）、配置管理网口。**

**适用场景**：服务器宕机后远程开关机、蓝屏/死机后看硬件日志定位原因、读温度/风扇/电源状态、配置带外管理 IP——这些都不依赖操作系统，只要 BMC 在线就能做。BMC 的背景见 `[[主板#BMC]]`。

---

### B. 两种使用方式

**① 本机本地调用**（在操作系统内，通过 `/dev/ipmi0`）：
```bash
ipmitool sensor list          # 本机直接读传感器
```
需加载 `ipmi_devintf`、`ipmi_si` 内核模块。

**② 远程调用**（从另外一台机器，通过网络访问目标服务器的 BMC）：
```bash
ipmitool -I lanplus -H 10.0.0.99 -U admin -P password <子命令>
```
*   `-I lanplus`：使用 IPMI 2.0 的 LAN 接口（比老 lan 安全）。
*   `-H`：BMC 的管理 IP（不是业务 IP）。
*   `-U` / `-P`：BMC 用户名和密码。

---

### C. 最常用的 6 个子命令

#### 1. `ipmitool power status` 【只读/安全】
*   **作用**：查询当前电源状态（on/off）。

#### 2. `ipmitool sensor list` 【只读/安全】
*   **作用**：读取所有传感器——温度、风扇转速、电压、电源功率。
*   **参数解释**：排查过热、风扇故障、电源异常的核心命令。温度过高（如 >80°C）要结合机房空调看。

#### 3. `ipmitool sel list` 【只读/安全】
*   **作用**：查看系统事件日志（SEL, System Event Log）。
*   **参数解释**：**故障排查金矿**。记录了硬件层面的事件：内存 CE/UE、电源掉电、温度告警、风扇故障、非正常关机。配 `[[内存#ECC报错]]` 定位内存故障槽位。

#### 4. `ipmitool chassis status` 【只读/安全】
*   **作用**：查看机箱状态——电源状态、是否开盖、前面板按钮状态、上次开机原因。

#### 5. `ipmitool power on|off|reset|cycle` 【危险/改变电源状态】
*   **作用**：远程开机、关机、硬复位、电源循环。
*   **参数解释**：⚠️ `power off` 是**硬断电**（相当于直接拔电），对业务是灾难性的；生产环境关机应优先走系统内 `shutdown -h now` 优雅关闭，只有系统完全失去响应时才用 IPMI 强制断电。

#### 6. `ipmitool lan print 1` 【只读/安全】
*   **作用**：查看 BMC 管理网口的配置（IP、掩码、网关、MAC）。
*   **参数解释**：配带外管理 IP 前先看现状。`1` 是通道号，通常是 1。

---

### D. 运维实战：服务器宕机的标准排查流程

**场景**：业务服务器 SSH 连不上、ping 不通，人不在机房。

```bash
# 1. 先确认 BMC 在线、电源状态
ipmitool -I lanplus -H <BMC_IP> -U <user> -P <pass> power status

# 2. 看传感器——是不是过热/电源挂了
ipmitool -I lanplus -H <BMC_IP> -U <user> -P <pass> sensor list | grep -i -E 'temp|fan|power'

# 3. 看事件日志——找最后一次异常
ipmitool -I lanplus -H <BMC_IP> -U <user> -P <pass> sel list | tail -20

# 4. 根据情况处理
ipmitool -I lanplus -H <BMC_IP> -U <user> -P <pass> power reset   # 硬重启
```

**日志解读示例**：SEL 里若出现 `Memory ECC Correctable Error, DIMM_A2` → 定位到 A2 槽内存报可纠正错误，呼应 `[[内存#ECC报错]]`，安排窗口更换；若出现 `Power Supply 2 Failure` → 冗余电源坏了一个，尽快更换（业务没断但要补冗余）。

---

### E. IDC 运维高危大坑（电源操作是双刃剑）

**坑一：`power off` 硬断电丢数据**
IPMI 的 `power off` 不经过操作系统，正在写盘的数据库/文件系统可能损坏。**除非系统已完全死透，否则永远先尝试优雅关机**：SSH 进去 `shutdown`，或用带外 KVM 登录后正常关机。

**坑二：带外管理口和业务网混淆**
BMC 管理口（专用管理网卡或共享网口）的 IP 和业务 IP 是两套。ping 通业务 IP 不代表 BMC 在线，反之亦然。配带外时要把管理网和业务网段隔离（安全要求），管理口接管理交换机。

**坑三：密码和网络安全**
IPMI 2.0 之前的老协议有安全漏洞，尽量用 `lanplus`。BMC 默认密码（如 admin/admin）必须改，且不要暴露在公网。这是 `[[运维红线]]` 里安全管理的要求。

---

### F. 常见误区与排错思路

1.  **误区：ping 不通服务器就以为它宕机了**
    *   **纠正**：先用 IPMI 看 `power status` 和 `sensor`，可能只是业务网卡/系统挂了，BMC 还活着、机器还在跑。盲目 `power reset` 反而中断业务。
2.  **误区：`ipmitool` 远程连不上 BMC**
    *   **排错**：① 确认 BMC IP 正确（`[[dmidecode]]` 或机身标签查）；② 确认管理网线插着、管理交换机 VLAN 对；③ 确认用户名密码；④ 目标机器必须通电（BMC 靠待机电）；⑤ 本机是否装了 ipmitool。
3.  **误区：SEL 日志看了不管**
    *   **纠正**：SEL 是硬件故障的早期预警。定期巡检导出分析，重复的 CE 报错要主动更换部件，别等彻底宕机。

---

### G. 小练习（自己动手试试）

1.  **（基础）** 在能登录的机器上执行 `ipmitool sensor list | head -20`，找出温度（Temperature）和风扇（Fan）传感器。
2.  **（进阶）** 执行 `ipmitool sel list | tail -10`，看看有没有历史告警事件，尝试读懂其中一条。
3.  **（排错）** 思考：如果一台服务器 ping 不通、SSH 也连不上，你会如何一步步用 IPMI 判断它是"真宕机"还是"只是系统卡死"？

---

### H. 是否继续深入？

`ipmitool` 是带外管理命令行，接下来你可以：
*   了解 iDRAC（Dell）/ iLO（HPE）/ BMC 的 Web 管理界面操作；
*   用 `[[dmidecode]]` 核对硬件 SN 和型号；
*   或整理一份"服务器宕机远程排查 SOP"。告诉我！
