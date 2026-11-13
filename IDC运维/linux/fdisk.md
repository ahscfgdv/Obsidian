你好！我是你的 Linux 命令行讲师。`fdisk` 是磁盘管理的核心工具，也是新手最容易“翻车”的地方。既然你在虚拟机里练习，那是最好的，我们可以放心大胆地试。

为了不让你一次性消化不良，我会严格按照你的结构逐步讲解，现在开始：

---

### A. 一句话定义和适用场景

**`fdisk` 是一个用于查看、创建、修改和删除磁盘分区表的交互式命令行工具。**
**适用场景**：当你为服务器或虚拟机添加了一块新硬盘（比如 `/dev/sdb`），需要把它划分成多个逻辑区域（分区）时；或者当你需要查看现有磁盘的分区结构（大小、类型、起始位置）时。

*注意：`fdisk` 主要针对传统的 MBR 分区表（通常限制单盘 2TB 以内）。如果是 GPT 分区表，虽然 CentOS 7 的 `fdisk` 也能看，但更推荐用 `gdisk` 或 `parted`。*

---

### B. 最常用的 5 个命令形式

#### 1. `fdisk -l` 【只读】
*   **作用**：列出系统中所有能被识别的磁盘及其分区的详细信息。
*   **参数解释**：`-l`（小写 L）代表 list（列出）。

```shell

[root@localhost ~]# fdisk -l

Disk /dev/sda: 42.9 GB, 42949672960 bytes, 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000b874e

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    83886079    40893440   8e  Linux LVM

Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

###
# Disk 磁盘物理信息
# Disk /dev/sda: 42.9 GB, 42949672960 bytes, 83886080 sectors
# /dev/sda: 第一块物理硬盘（系统盘）
# 42.9 GB: 总容量
# 42949672960 bytes: 精确字节数
# 83886080 sectors: 总扇区数（字节数 ÷ 512）

# Units = sectors of 1 * 512 = 512 bytes
# Units: 单位。表示下方分区表中的数字以“扇区”为单位，1扇区=512字节

# Sector size (logical/physical): 512 bytes / 512 bytes
# 扇区大小（逻辑/物理）：传统硬盘通常都是 512/512

# I/O size (minimum/optimal): 512 bytes / 512 bytes
# I/O 大小（最小/最优）：读写块大小

# Disk label type: dos
# 分区表类型：dos 代表 MBR 分区表（最大支持 2TB，最多 4 个主分区）

# Disk identifier: 0x000b874e
# 磁盘标识符（MBR 磁盘的唯一 ID）

# 分区表详情（表格）
#   Device Boot      Start         End      Blocks   Id  System
# /dev/sda1   *        2048     2099199     1048576   83  Linux
# /dev/sda2         2099200    83886079    40893440   8e  Linux LVM

# Device: 分区名称（如 /dev/sda1 是第一块盘的第一个分区）
# Boot: 启动标志，带 * 号表示活动分区（BIOS 从这里找引导程序）
# Start: 分区起始扇区号
# End: 分区结束扇区数
# Blocks: 分区占用的块数量（1块=1KB）
# Id: 分区类型的十六进制代码（83=标准Linux分区，8e=Linux LVM）
# System: Id 对应的可读名称

# ---------------------------------------------------------
# 第二块物理硬盘（新加的空盘，尚未初始化，无分区表）
# ---------------------------------------------------------
# Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
# /dev/sdb: 第二块硬盘（常用于测试或扩容）
# 5368 MB: 总容量（约 5GB）

# Units = sectors of 1 * 512 = 512 bytes
# Sector size (logical/physical): 512 bytes / 512 bytes
# I/O size (minimum/optimal): 512 bytes / 512 bytes

# 注意：/dev/sdb 下方没有任何分区信息，说明它是一块全新的裸盘。
# 可以直接用 fdisk /dev/sdb 进行分区练习。
Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
###

```

#### 2. `fdisk -l /dev/sdb` 【只读】
*   **作用**：仅列出指定磁盘（此处为 `/dev/sdb`）的分区信息。
*   **参数解释**：后面直接跟设备路径，用于精准查看。在不确定自己插的是哪块盘时，非常有用。

#### 3. `fdisk /dev/sdb` 【有风险】
*   **作用**：进入针对 `/dev/sdb` 这块磁盘的交互式操作界面。
*   **参数解释**：直接接设备名。进入后，你可以输入 `m`（帮助）、`p`（打印分区表）、`n`（新建）、`d`（删除）、`w`（保存写盘）、`q`（不保存退出）等交互指令。

#### 4. `fdisk -u /dev/sdb` 【有风险】
*   **作用**：进入交互模式，但在显示和接受扇区时，以“扇区（Sector）”为单位，而不是传统的“柱面（Cylinder）”。
*   **参数解释**：`-u` 代表 units。现代大容量磁盘通常按扇区对齐更精准。

#### 5. `fdisk -s /dev/sdb1` 【只读】
*   **作用**：显示指定分区（此处为 `sdb1`）的大小（以 512 字节的块为单位）。
*   **参数解释**：`-s` 代表 size。*(讲师注：此参数在较新的 Linux 版本中已被废弃，但在 CentOS 7 的 `fdisk` 中依然可用，知道即可，日常更推荐用 `lsblk` 查看)*。

---

### C. “只读安全”示例：查看信息

**目标**：安全地查看帮助和磁盘信息，绝不修改任何数据。

**1. 查看帮助**
```bash
# 查看命令帮助，了解所有参数
fdisk --help
```

**2. 查看所有磁盘信息**
```bash
sudo fdisk -l
```

**典型输出解读**：
```text
Disk /dev/sda: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000a1b2c

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
```
*   **Disk /dev/sda**：这块盘的容量和字节数。
*   **Sector size**：扇区大小，1个扇区 512 字节。
*   **Disk label type: dos**：表示这是一块 MBR 分区表（dos 代表 MBR）。
*   **Device**：分区名，如 `/dev/sda1`。
*   **Boot**：带 `*` 表示这是引导分区。
*   **System**：分区类型（83 代表标准的 Linux 分区）。

---

### D. “危险操作”示例：新建与删除

🚨 **严重警告**：以下操作会修改磁盘分区表！操作前**必须确认设备名**。如果你用 `fdisk /dev/sda` 对系统盘进行删除操作并保存，系统将直接崩溃无法启动。**强烈建议在虚拟机中挂载一块未使用的虚拟磁盘（如 `/dev/sdb`），或使用 `dd`/`truncate` 创建的循环设备（loop device）进行练习。**

**安全准备**：先用 `lsblk` 确认你要操作的是空盘（如 `/dev/sdb`），**绝对不要对 `/dev/sda` 操作**。

```bash
# 进入交互界面（假设 /dev/sdb 是你要练习的空白测试盘）
sudo fdisk /dev/sdb
```

交互过程及解释：
```text
Command (m for help): p   # 打印当前分区表，确认是空盘（或确认没有重要数据）
Command (m for help): n   # 新建一个分区
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p     # 选择创建主分区
Partition number (1-4, default 1): 1  # 分区号为1
First sector (2048-20971519, default 2048): [直接回车] # 起始扇区，默认即可
Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519): +2G  # 设定大小，这里分配2G

Command (m for help): p   # 再次打印，你会看到刚创建的 /dev/sdb1 信息，但此时尚未生效！
Command (m for help): w   # 保存并退出（危险动作，只有输入 w 回车后，修改才会真正写入物理磁盘）
```

**确认与备份**：如果你是在真实的服务器上操作，操作前必须向客户或主管确认工单，并对重要数据做备份。`w` 敲下去回车后，没有任何后悔药。

---

### E. 常见误区与排错思路

1.  **误区：改完分区表，马上 `mkfs` 格式化却报错。**
    *   **原因**：`fdisk` 只是更改了磁盘上的“图纸”，Linux 内核还没更新最新的分区表信息。
    *   **排错**：执行 `partprobe /dev/sdb` 或者重启系统，让内核重新读取分区表。
2.  **误区：把 fdisk 和 mkfs 搞混。**
    *   **原因**：认为 fdisk 就是格式化。
    *   **排错**：`fdisk` 是划地皮（建分区），`mkfs` 是建房子（建文件系统，如 `mkfs.ext4`），最后还要 `mount`（挂载）才能用。
3.  **误区：尝试用 fdisk 划分大于 2TB 的磁盘。**
    *   **原因**：MBR 分区表最大只支持 2TB。
    *   **排错**：大于 2TB 的盘请在 CentOS 7 下使用 `parted` 工具，并在提示时选择 `gpt` 格式。
4.  **排错思路：显示“Device or resource busy”**。
    *   **原因**：你正尝试对一块已经被挂载（mount）的磁盘或正在使用的分区进行删除或修改。
    *   **解决**：必须先 `umount` 卸载该磁盘，或者确认没有进程在占用它（用 `lsof /dev/sdb1` 查）。

---

### F. 小练习（先不要看答案，自己动手试试）

1.  **（基础）** 使用 `fdisk` 命令进入交互界面（选择你的测试盘，例如 `/dev/sdb`），输入 `m` 查看帮助菜单，找到并输入哪个字母可以查看当前磁盘的“分区类型”？
2.  **（进阶）** 在测试盘（如 `/dev/sdb`）中，尝试新建一个大小为 1G 的主分区，但在最后一步**不要输入 `w`**，而是输入 `q`（不保存退出）。退出后，再次执行 `fdisk -l /dev/sdb`，看看分区还在不在？
3.  **（排错）** 假设你在测试盘中建好了一个分区 `/dev/sdb1`，你尝试运行 `mkfs.ext4 /dev/sdb1` 却提示找不到该设备，你应该用什么命令刷新内核的分区表？

---

### G. 是否继续深入？

你可以先在自己的虚拟机上完成上面的练习（这是零风险的）。
完成后，你可以告诉我：
*   你想不想了解如何用 `parted` 工具来管理大于 2TB 的磁盘？
*   你想不想了解如何创建一个“虚拟盘（loop 设备）”来安全地无限次练习 `fdisk`？
*   或者你有哪个具体的输出没看懂？

随时告诉我，我们继续！