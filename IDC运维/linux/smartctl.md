你好！我是你的 Linux 命令行讲师。`smartctl` 是硬盘的"健康体检医生"。硬盘是最容易坏的部件，而 SMART 技术能在硬盘彻底罢工前发出预警。定期用 `smartctl` 体检、及时更换亚健康盘，是 IDC 运维避免数据事故的关键动作。

---

### A. 一句话定义和适用场景

**`smartctl` 是一个读取和控制硬盘 SMART（Self-Monitoring, Analysis and Reporting Technology，自我监测分析与报告技术）属性的命令，用于检测硬盘健康状态、预测故障。**

**适用场景**：服务器硬盘巡检、怀疑某块盘快坏了、RAID 阵列盘掉了想确认原因、新盘到货验健康——都用 `smartctl`。它属于 `smartmontools` 包，需 `yum install smartmontools`。硬盘 SMART 的基础知识见 `[[硬盘#参数]]`。

---

### B. 最常用的 5 个命令形式

#### 1. `smartctl -a /dev/sda` 【只读/安全】
*   **作用**：显示硬盘的全部 SMART 信息（健康状态 + 属性表 + 自检日志）。`-a` = all，最常用。
*   **参数解释**：`/dev/sda` 用实际盘符（先用 `[[lsscsi]]` 或 `[[lsblk]]` 确认）。

#### 2. `smartctl -H /dev/sda` 【只读/安全】
*   **作用**：只查硬盘的 SMART 总体健康结论（PASSED / FAILED）。
*   **参数解释**：`-H` = health。快速巡检用，一行出结果。⚠️ 但只看它不够——很多盘 "PASSED" 但关键属性已经恶化。

#### 3. `smartctl -x /dev/sda` 【只读/安全】
*   **作用**：显示扩展信息，含 NVMe SSD 的寿命、通电时间、错误日志。
*   **参数解释**：`-x` = extended。SSD/NVMe 盘用它能看剩余寿命百分比。

#### 4. `smartctl -t short /dev/sda` 【会启动自检/谨慎】
*   **作用**：启动一次短时 SMART 自检（约 1-2 分钟）。
*   **参数解释**：还有 `-t long`（长时，可能几小时）。**自检是只读检测，不改数据**，但会占用磁盘 IO，生产高峰别跑。

#### 5. `smartctl -l selftest /dev/sda` 【只读/安全】
*   **作用**：查看自检历史结果（每次自检是否通过）。
*   **参数解释**：配合 `-t` 使用——跑完自检后用这个看结果。

> 💡 **RAID 卡下的特殊用法**：如果盘挂在 RAID 卡后面，`smartctl` 可能读不到，要用 `-d` 指定设备类型，如 `smartctl -a -d megaraid,0 /dev/sda`（看 RAID 卡下第 0 块盘）。具体类型看 RAID 卡型号。

---

### C. "只读安全"示例：读懂 SMART 属性表

执行 `smartctl -a /dev/sda`（节选）：

```text
SMART overall-health self-assessment test result: PASSED

ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      RAW_VALUE
  5 Reallocated_Sector_Ct   0x0033   100   100   010    Pre-fail  0
  9 Power_On_Hours          0x0032    92    92   000    Old_age   12345
 12 Power_Cycle_Count       0x0032    99    99   000    Old_age   312
197 Current_Pending_Sector  0x0032   100   100   000    Old_age   0
198 Offline_Uncorrectable   0x0030   100   100   000    Old_age   0
```

**关键属性解析（面试和巡检重点盯这几个）**：
*   **ID 5 `Reallocated_Sector_Ct`（重映射扇区数）**：硬盘发现坏扇区后，用备用扇区替换的计数。**RAW_VALUE 非零且在增长 = 盘在恶化**，快坏了。
*   **ID 197 `Current_Pending_Sector`（待映射扇区）**：发现了坏扇区但还没成功重映射。**这个非零很危险**，说明有读不出来的扇区。
*   **ID 198 `Offline_Uncorrectable`（不可纠正错误）**：无法修复的坏扇区数。增长 = 数据可能已损坏。
*   **ID 9 `Power_On_Hours`（通电时间）**：`12345` 小时，判断盘的新旧程度。
*   **VALUE / WORST / THRESH**：规范化值，只要 VALUE 掉到 THRESH（阈值）以下就算失败。**Pre-fail 类型属性要重点盯**——它们预示即将故障。

**💡 判断口诀**：`-H` 显示 PASSED 不代表万事大吉。**重点看 5/197/198 的 RAW_VALUE 是否为 0、是否随巡检增长**。这三个只要涨，就要备盘、安排更换。

**SSD/NVMe 特殊项**：`-x` 里的 `Media_Wearout_Indicator` / `Percentage Used` 显示寿命消耗百分比，到 100% 就该换。

---

### D. IDC 运维高危大坑（体检结果要会用）

**坑一：只看 PASSED 就放心**
很多盘 `-H` 报 PASSED，但 197/198 已经在涨。等它真正 FAILED 时往往已经来不及。**巡检要看趋势**：建立基线，每次记录关键属性的 RAW_VALUE，发现增长立即处理。

**坑二：自检/坏盘操作触发进一步损坏**
对一块已经有大量坏扇区的盘跑 `-t long` 长时自检，会反复读取坏区，可能让本已脆弱的盘彻底挂掉。**如果盘在 RAID 里已经降级，不要对它做激烈操作，先备份/更换**。

**坑三：热插拔换盘不确认**
换盘前必须用 `[[lsscsi]]` + `smartctl` 的序列号确认是哪块物理盘（`smartctl -a /dev/sdX | grep -i serial`），防止拔错好盘。生产环境走 RAID 卡管理流程，遵守 `[[运维红线]]`。

---

### E. 常见误区与排错思路

1.  **误区：SSD 也看"重映射扇区"那套**
    *   **纠正**：SSD/NVMe 的 SMART 属性集合和机械盘不同，重点看寿命（Percentage Used）、不可用块、CRC 错误。用 `smartctl -x` 看完整信息，呼应 `[[硬盘#固态硬盘]]`。
2.  **误区：SMART 说好就一定不会坏**
    *   **纠正**：SMART 是"预测"，不是"保证"。有相当比例的硬盘是突然暴毙、SMART 毫无征兆的。**所以 RAID / 备份才是根本保障**，SMART 只是辅助手段。
3.  **误区：USB 硬盘盒里的盘读不到 SMART**
    *   **纠正**：很多 USB 桥接芯片不透传 SMART，`smartctl -a /dev/sdb` 会失败。需要 `-d sat` 指定 SAT 协议，或直接把盘接到 SATA/SAS 口。

---

### F. 小练习（自己动手试试）

1.  **（基础）** 执行 `smartctl -H /dev/sda`（或你的盘符），看看健康结论是什么。
2.  **（进阶）** 执行 `smartctl -a /dev/sda | grep -E 'Reallocated|Pending|Uncorrectable|Power_On'`，记录这几个关键属性的 RAW_VALUE。
3.  **（排错）** 思考：一块盘 `-H` 显示 PASSED，但 `Current_Pending_Sector` 从 0 涨到了 5，你会怎么处理？为什么不能只看 PASSED？

---

### G. 是否继续深入？

`smartctl` 查硬盘健康，接下来你可以：
*   用 `[[lsscsi]]` / `[[lsblk]]` 确认盘符和物理盘位对应；
*   用 `[[mdadm]]` 了解软件 RAID 的硬盘健康管理；
*   或整理一份"硬盘巡检 + 换盘标准流程"。告诉我！
