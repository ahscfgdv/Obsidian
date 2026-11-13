#### 适用场景与安全红线

本 SOP 适用于在 CentOS 7 系统中，将一块**全新的、尚未被系统使用的物理磁盘**（或云盘扩容后的新分区）挂载到操作系统目录中，以便业务能够存取数据。

🚨 **绝对红线**：操作前必须使用 `lsblk` 或 `fdisk -l` 确认目标磁盘，**严禁对系统盘（通常是 `/dev/sda`）执行分区或格式化操作**，否则系统将直接崩溃、数据永久丢失。如果在云环境或 IDC 生产环境中，请确保已获授权并完成数据备份。

---

#### 第一阶段：环境检查与设备确认

**1. 确认磁盘是否被系统识别**
```bash
# 查看当前系统的块设备树状图
lsblk
```
*预期结果*：找到新插入的盘（假设是 `/dev/sdb`）。注意看它的 `MOUNTPOINT` 列和它下面有没有子节点。如果是一块裸盘，它下面没有任何 `sdb1`、`sdb2` 这样的分区。

**2. 确认分区表类型**
```bash
# 查看指定磁盘的详细信息
fdisk -l /dev/sdb
```
*预判*：确认它是 MBR（`dos`）还是 GPT（`gpt`）。如果是小于 2TB 的盘，用 MBR 即可；若大于 2TB，必须使用 `parted` 或 `gdisk` 建立 GPT 分区表。

---

#### 第二阶段：磁盘分区

**1. 进入 fdisk 交互界面**
```bash
# 对空盘 /dev/sdb 进行分区操作
fdisk /dev/sdb
```

**2. 交互式操作步骤**
进入界面后，按以下顺序输入指令：
```text
Command (m for help): n       # n 代表新建分区
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p         # p 代表创建主分区
Partition number (1-4, default 1): 1  # 分区号，默认1即可
First sector (2048-xxxxx, default 2048): [直接回车] # 起始扇区，使用默认值（保证1MB对齐）
Last sector, +sectors or +size{K,M,G} (2048-xxxxx, default xxxxx): [直接回车] # 结束扇区，直接回车代表把全盘空间都分给这个区。如果只想分 2TB，输入 +2T

Command (m for help): p       # 打印确认一下分区信息是否创建成功
Command (m for help): w       # 写入并退出
```

**3. 让内核重新识别分区表**
```bash
# 刷新内核分区表（如果提示找不到设备时执行）
partprobe /dev/sdb
# 再次查看，应该能看到 /dev/sdb1 了
lsblk
```

---

#### 第三阶段：格式化文件系统

CentOS 7 推荐的默认文件系统是 **XFS**（处理大文件性能好），也可以选择 EXT4。

```bash
# 将 /dev/sdb1 格式化为 XFS 文件系统
mkfs.xfs /dev/sdb1

# （可选）如果业务明确要求使用 EXT4，则执行：
# mkfs.ext4 /dev/sdb1
```
*注意*：格式化会清除磁盘上的所有数据，执行前请再次确认盘符是否绝对正确。

---

#### 第四阶段：挂载与验证

**1. 创建挂载点目录**
```bash
# 假设我们要把这块盘挂载到 /data 目录
mkdir -p /data
```

**2. 临时挂载（重启后会失效）**
```bash
mount /dev/sdb1 /data
```

**3. 验证挂载结果**
```bash
# 查看容量和挂载点
df -Th
```
*预期结果*：输出中应包含一行 `/dev/sdb1 xfs [容量] [已用] [可用] 1% /data`。

---

#### 第五阶段：配置开机自动挂载（关键步骤）

**千万不要直接在 `/etc/fstab` 中写 `/dev/sdb1`**，因为服务器的磁盘顺序可能会因插拔而改变（比如插拔了一块系统盘，sdb 可能会变成 sdc），导致重启时由于找不到盘而**无法开机**。必须使用**UUID**。

**1. 获取分区的 UUID**
```bash
# 查看 sdb1 的 UUID 和文件系统类型
blkid /dev/sdb1
# 输出示例：/dev/sdb1: UUID="f3b1a2c4-1234-5678-90ab-cdef01234567" TYPE="xfs"
```
*复制 UUID 字段里的字符，不要带引号。*

**2. 编辑 /etc/fstab**
```bash
vi /etc/fstab
```

在文件末尾添加一行（请替换为你刚刚复制的 UUID）：
```ini
UUID=f3b1a2c4-1234-5678-90ab-cdef01234567 /data xfs defaults 0 0
```
*字段解释*：
*   `UUID=...`：设备标识。
*   `/data`：挂载点。
*   `xfs`：文件系统类型。
*   `defaults`：挂载参数（读写、允许执行等）。
*   `0`：是否使用 `dump` 备份（0代表不备份）。
*   `0`：开机时是否使用 `fsck` 检查（根目录通常写1，其他目录写0）。

**3. 测试 fstab 文件是否写错（救命操作）**
```bash
# 先卸载刚才的临时挂载
umount /data

# 重新加载 fstab 文件中的所有挂载项（如果报错，千万不要重启！立刻回去改 fstab）
mount -a

# 再次验证是否挂载成功
df -h /data
```

---

#### 第六阶段：常见故障排查

**1. 报错 `mount: wrong fs type, bad option, bad superblock`**
*   **原因**：没有格式化就直接挂载了，或者文件系统类型写错了。
*   **解决**：先 `lsblk -f` 确认是否有 FSTYPE，如果没有，先执行 `mkfs.xfs`。

**2. 重启后服务器进入紧急模式（Emergency Mode）**
*   **原因**：`/etc/fstab` 写错了（比如 UUID 写错、挂载点写错），系统在开机时找不到这块盘，卡死了。
*   **解决**：在控制台输入 root 密码进入系统，执行 `vi /etc/fstab` 删除或注释掉错误的那行，重启即可。

**3. `mount: /data: target is busy`（无法卸载）**
*   **原因**：有进程正在占用 `/data` 目录。
*   **解决**：执行 `lsof /data` 查看占用进程，或用 `fuser -mv /data` 找出占用者并终止相关进程，然后再 `umount`。

#### 第七阶段：环境重置——取消挂载与清理磁盘

在进行实验验证后，或者需要将磁盘拔出移作他用时，必须按顺序执行以下操作，将系统与磁盘彻底恢复到初始的“裸盘”状态。**⚠️ 注意：以下操作将永久销毁该磁盘上的数据，请务必确认盘符（如 `/dev/sdb`）正确！**

**1. 取消目录挂载**
```bash
# 卸载挂载点（如果提示 target is busy，说明有进程占用，需先退到 / 目录或重启系统）
umount /data

# 如果死活卸载不掉，可以使用懒卸载（强制断开引用，但进程可能仍持有句柄）
# umount -l /data
```

**2. 清理开机自动挂载配置（极其关键）**
```bash
# 编辑 fstab 文件
vi /etc/fstab

# 将之前添加的对应行（包含 UUID=xxxx /data xfs ...）整行删除，保存并退出。
# 重新加载 fstab，验证没有语法错误
mount -a
```

**3. 擦除文件系统签名（抹除格式化痕迹）**
仅仅删除分区有时不够，系统底层依然会残留 `xfs` 或 `ext4` 的元数据签名。使用 `wipefs` 抹除最干净：
```bash
# 擦除分区的文件系统签名
wipefs -a /dev/sdb1
```

**4. 删除磁盘分区**
```bash
# 进入 fdisk 交互界面
fdisk /dev/sdb

# 在交互界面中依次输入：
Command (m for help): d       # d 代表 delete（删除分区）
Selected partition 1          # 确认删除 1 号分区
Command (m for help): w       # w 代表写入并退出
```

**5. 刷新内核并验证**
```bash
# 让内核重新读取分区表
partprobe /dev/sdb

# 再次查看，此时应该只剩下光秃秃的 /dev/sdb，底下的 sdb1 已经消失
lsblk
```
执行完以上步骤，这块磁盘就如同刚出厂挂载到服务器上一样，没有任何分区和文件系统，可以随时拔下用于其他服务器。