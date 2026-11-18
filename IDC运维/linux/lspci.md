你好！我是你的 Linux 命令行讲师。`lspci` 是盘点服务器"肚子里到底插了什么硬件"的第一把钥匙。装机验收、故障排查、扩容前核实，都离不开它。

我们按照老规矩 A-G 结构来学。

---

### A. 一句话定义和适用场景

**`lspci`（list PCI）是一个用于列出本机所有 PCI / PCIe 总线以及挂在这些总线上的设备的命令。**

**适用场景**：网卡、显卡、RAID 卡、HBA 卡、NVMe 转接卡等都挂在 PCI 总线上。当你需要确认"服务器实际识别到几块网卡"、"新加的 RAID 卡系统认没认到"、"这块网卡是什么芯片型号（好找驱动）"时，敲 `lspci`。

---

### B. 最常用的 5 个命令形式

#### 1. `lspci` 【只读/安全】
*   **作用**：列出所有 PCI 设备的简要信息（总线号 + 设备类别 + 厂商型号）。
*   **参数解释**：无参数直接运行，输出按总线编号排序。

#### 2. `lspci -nn` 【只读/安全】
*   **作用**：额外显示设备 ID 和厂商 ID，形如 `[8086:1528]`。
*   **参数解释**：`-nn` 两个 n 分别代表把厂商名、设备名也显示成数字 ID。**查驱动兼容、确认芯片型号时最有用**——有了 `[8086:1528]` 这种 ID，去搜驱动一搜一个准。

#### 3. `lspci | grep -i -E 'ether|net'` 【只读/安全】
*   **作用**：只筛出网卡设备（Ethernet controller / Network controller）。
*   **参数解释**：`-i` 忽略大小写，`-E` 用正则匹配多个关键词。快速数"这机器几块网卡"。

#### 4. `lspci -vvv` 【只读/安全】
*   **作用**：显示极详细信息，包括驱动、带宽、链路宽度、NUMA 节点。
*   **参数解释**：`v` 越多越详细（v / vv / vvv）。排查网卡速率上不去、PCIe 降速时看 `LnkSta`（链路状态）。

#### 5. `lspci -t` 【只读/安全】
*   **作用**：以树状图显示 PCI 总线的层级拓扑（哪个桥下面挂了哪些设备）。
*   **参数解释**：`-t` = tree。理解设备挂在哪条总线、是否共享带宽时用。

---

### C. "只读安全"示例：读懂输出

执行 `lspci`：

```text
[root@localhost ~]# lspci
00:00.0 Host bridge: Intel Corporation Xeon E7 v3/Xeon E5 v3/Core i7 DMI2
00:02.0 PCI bridge: Intel Corporation Xeon E5 v3 PCI Express Root Port
00:19.0 Ethernet controller: Intel Corporation Ethernet Connection I217-LM
01:00.0 Ethernet controller: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection
02:00.0 RAID bus controller: LSI Logic / Symbios Logic MegaRAID SAS-3 3108
03:00.0 VGA compatible controller: ASPEED Technology ASPEED AST2000 graphics
```

**字段解析**：
*   **`00:19.0`**：设备地址，格式为 `总线:设备.功能`（bus:device.function）。这是设备的"门牌号"。
*   **设备类别**：`Ethernet controller`（网卡）、`RAID bus controller`（RAID 卡）、`VGA compatible controller`（显卡，这里是 ASPEED 远程管理显卡）。
*   **厂商 + 型号**：`Intel 82599ES` 是经典万兆网卡；`LSI MegaRAID 3108` 是常见服务器 RAID 卡；`ASPEED AST2000` 是 BMC 的板载显卡（说明有带外管理）。

**💡 讲师提示**：加 `-nn` 后，`82599ES` 会显示成 `[8086:10fb]`——`8086` 是 Intel 的厂商 ID，`10fb` 是这块卡的型号 ID。

---

### D. IDC 运维高危大坑（lspci 只读，但结论要用对）

`lspci` 本身零风险，但它检测的是**PCI 总线层面**。有个经典坑：

**场景**：新插了一块网卡，`lspci` 能看到设备，但系统里 `ip link` 看不到对应网口（如 `eth0`/`ens1f0`）。
**原因**：设备被总线识别了，但**驱动没加载**，或者被 `vfio`/`pci-stub` 之类的驱动接管了（常见于做了 SR-IOV 或显卡直通的机器）。
**排查**：
1. `lspci -nnk` 看设备用的驱动（`Kernel driver in use:` 一行）。
2. `dmesg | grep -i eth` 看内核有没有报驱动错误。
3. 确认驱动模块：`lsmod | grep ixgbe`（82599 用 ixgbe 驱动）。

反过来也成立：**`lspci` 里完全看不到设备**，那多半是硬件层面——没插到位、 PCIe 槽禁用、或 BIOS 里把该槽关了，这时候要重启进 BIOS 或在带外管理里看，而不是死磕系统命令。

---

### E. 常见误区与排错思路

1.  **误区：`lspci` 看不到盘就以为硬盘坏了**
    *   **纠正**：普通 SATA/SAS 硬盘挂在 SCSI/SAS 控制器上，用 `lspci` 只能看到最上层的 RAID 卡/HBA 卡，看不到每一块盘。看单块盘请用 `[[lsscsi]]` 或 `[[lsblk]]`，看健康用 `[[smartctl]]`。
2.  **误区：把板载 ASPEED 显卡当成独立显卡**
    *   **纠正**：看到 `ASPEED ASTxxx` 不用紧张，那是 BMC 带外管理的远程显示芯片，不是业务显卡，详见 `[[服务器]]`。
3.  **误区：设备名 `eth0` / `ens1f0` 会在重启后漂移**
    *   **纠正**：现代 Linux 用 systemd 的"可预测命名"（en=以太网，s1=插槽1，f0=端口0）。`lspci` 的地址能帮你把"物理槽位"和"系统设备名"对上号，做网口绑定（bonding）时必须核对清楚。

---

### F. 小练习（自己动手试试）

1.  **（基础）** 执行 `lspci | grep -i ether`，数一下你的机器有几块网卡，分别是什么芯片？
2.  **（进阶）** 执行 `lspci -nn`，找出网卡那行的 `[xxxx:xxxx]` 设备 ID，猜猜厂商 ID `8086` 是哪家公司？
3.  **（排错）** 执行 `lspci -vvv | grep -A5 -i ethernet`，找到 `LnkSta:` 一行，看看链路速率是 2.5GT/s、5GT/s 还是 8GT/s，思考它和网卡标称速率的关系。

---

### G. 是否继续深入？

`lspci` 管"总线设备"，接下来你可以：
*   用 `[[lscpu]]` 看 CPU 的槽位/核心/NUMA 拓扑；
*   用 `[[dmidecode]]` 读 SN、型号、内存槽，做完整资产盘点；
*   或者深挖 PCIe 链路降速的排查思路。告诉我！
