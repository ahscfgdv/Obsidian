你好！我是你的 Linux 命令行讲师。`lsscsi` 是查看 SCSI / SAS / SATA 设备的"硬盘花名册"。在服务器尤其是带 RAID 卡、SAS 背板的机器上，它比 `lsblk` 更贴近底层总线视角，能帮你把"物理盘位"和"系统盘符"对上号。

---

### A. 一句话定义和适用场景

**`lsscsi`（list SCSI）是一个列出本机所有 SCSI 类设备（含 SAS、SATA、SSD，以及 RAID 卡逻辑盘）及其对应 `/dev/sdX` 盘符的命令。**

**适用场景**：服务器硬盘盘点、确认某块盘挂在哪条总线、把故障盘的盘符和物理槽位对应起来、配合 `[[smartctl]]` 查健康前先确定设备名——这些场景都用它。注意多数服务器默认没装，需 `yum install lsscsi` 或 `apt install lsscsi`。

---

### B. 最常用的 5 个命令形式

#### 1. `lsscsi` 【只读/安全】
*   **作用**：列出所有 SCSI 设备的总线地址、类型、厂商型号、盘符。
*   **参数解释**：无参数直接运行。

#### 2. `lsscsi -s` 【只读/安全】
*   **作用**：在输出中增加每个设备的容量（size）。
*   **参数解释**：`-s` = size。盘点"这块盘多大"时最常用。

#### 3. `lsscsi -g` 【只读/安全】
*   **作用**：显示对应的 `/dev/sgX` 通用设备节点。
*   **参数解释**：`-g` = generic。像 `smartctl`、`sg_*` 这类工具操作某些 SAS 盘时要用 `/dev/sgX` 而不是 `/dev/sdX`。

#### 4. `lsscsi -t` 【只读/安全】
*   **作用**：显示传输协议类型（sata / sas / spi 等）。
*   **参数解释**：`-t` = transport。判断这块盘走的是 SATA 还是 SAS 协议。

#### 5. `lsscsi -H` 【只读/安全】
*   **作用**：只列出 SCSI 主机适配器（HBA/RAID 卡），不列下面的盘。
*   **参数解释**：`-H` = hosts。快速看有几块 HBA/RAID 卡，呼应 `[[lspci]]`。

---

### C. "只读安全"示例：读懂输出

执行 `lsscsi -s`：

```text
[root@localhost ~]# lsscsi -s
[0:0:0:0]    disk    LSI      MR9361-8i         4.68  /dev/sda   1.09TB
[0:0:1:0]    disk    LSI      MR9361-8i         4.68  /dev/sdb   1.09TB
[0:2:0:0]    disk    SEAGATE  ST4000NM0023      B001  /dev/sdc   4.00TB
[0:2:1:0]    disk    SEAGATE  ST4000NM0023      B001  /dev/sdd   4.00TB
[6:0:0:0]    cd/dvd  HL-DT-ST DVDROM DH18NS40  1.02  /dev/sr0        -
```

**字段解析**：
*   **`[host:channel:id:lun]`**：SCSI 地址四元组。`[0:2:1:0]` 表示 host 0、通道 2、目标 ID 1、LUN 0。这是设备在 SCSI 总线上的"坐标"。
*   **类型**：`disk`（磁盘）、`cd/dvd`（光驱）、`tape`（磁带机）。
*   **厂商 + 型号**：`SEAGATE ST4000NM0023` 是希捷 4T 企业盘；前面两块 `LSI MR9361-8i` 是 **RAID 卡做出的逻辑盘**（虚拟磁盘），说明 `/dev/sda`、`/dev/sdb` 其实是 RAID 组，不是单块物理盘。
*   **盘符**：`/dev/sdc` 等，这就是 `[[smartctl]]`、`[[fdisk]]` 要用的设备名。

**💡 讲师提示**：注意区分"物理盘"和"RAID 逻辑盘"。`lsscsi` 里厂商显示 `LSI`/`MegaRAID` 的多半是 RAID 卡虚拟出来的盘；真正的物理盘在 RAID 卡下面，有时要用 `megacli`/`storcli` 或 `lsscsi` 配合 RAID 卡工具才能看到每一块。

---

### D. IDC 运维高危大坑（lsscsi 只读，但要会用对场景）

**场景：换盘时找错盘符**
这是运维事故高发点。`lsscsi` 显示的 `/dev/sdX` **盘符在重启后可能变化**（尤其热插拔或盘位变动后）。如果误把好盘的盘符当成坏盘拔掉，会造成数据灾难。

**正确做法**：
1. **不要只凭盘符操作**——拔盘前用多重信息交叉确认：`lsscsi -s`（容量）、`[[smartctl]] -a /dev/sdX`（看序列号 Serial Number）、硬盘指示灯定位（盲插/定位灯）、RAID 卡管理工具（storcli/megacli）看物理盘槽位。
2. **确认序列号**：`smartctl -a /dev/sdX | grep -i serial`，和故障工单上的 SN 对上，再动手。
3. **生产环境拔盘走流程**：先在 RAID 卡里 offline 该盘、确认热备盘接管或阵列降级可承受，再拔。遵守 `[[运维红线]]`。

---

### E. 常见误区与排错思路

1.  **误区：`lsscsi` 和 `[[lsblk]]` 分不清**
    *   **纠正**：`lsblk` 展示的是**块设备树**（盘 → 分区 → LVM），偏文件系统视角；`lsscsi` 展示的是 **SCSI 总线设备**，偏底层硬件视角，能看到 `/dev/sgX` 和光驱/磁带。一般先看 `lsblk` 看分区挂载，需要总线信息/通用设备节点时用 `lsscsi`。
2.  **误区：命令不存在就以为没盘**
    *   **纠正**：`lsscsi` 很多系统默认没装，`command not found` 不代表没硬盘。用 `[[lsblk]]` 或 `cat /proc/scsi/scsi` 替代。
3.  **误区：把 RAID 逻辑盘当单盘做 SMART**
    *   **纠正**：`smartctl -a /dev/sda` 如果 sda 是 RAID 虚拟盘，可能读不到真实 SMART 信息。要用 `-d` 指定 RAID 卡下的直通设备类型，或用 RAID 卡工具查看物理盘健康。

---

### F. 小练习（自己动手试试）

1.  **（基础）** 执行 `lsscsi -s`，看看你的机器有几块盘、分别多大、厂商是什么。
2.  **（进阶）** 执行 `lsscsi -t`，观察每块盘的传输协议是 sata 还是 sas，思考和企业级 SAS 盘相比的差异。
3.  **（排错）** 对比 `lsscsi` 和 `[[lsblk]]` 的输出，找出哪些设备是 RAID 卡虚拟出来的逻辑盘（提示：看厂商字段）。

---

### G. 是否继续深入？

`lsscsi` 列硬盘设备，接下来你可以：
*   用 `[[smartctl]]` 深入查每块盘的 SMART 健康；
*   用 `[[fdisk]]` / `[[lsblk]]` 看分区与挂载；
*   或整理一份"硬盘故障换盘标准流程"。告诉我！
