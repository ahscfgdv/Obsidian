## 在虚拟机添加6块硬盘

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20261110164731055_2026-11-10_164731.png)


在 CentOS 7 虚拟机中做 RAID 0 实验是非常经典的练习。我们将使用 Linux 内核自带的**软件 RAID**工具 `mdadm` 来完成。

以下是从零开始的完整实验步骤，请在操作前**确保虚拟机的系统盘（通常是 `/dev/sda`）有足够的剩余空间，或者直接在虚拟机管理软件中新增两块空白虚拟硬盘**。

---

## Raid0实验

### 🛠️ 第一阶段：准备工作

**1. 添加虚拟硬盘**
*   **如果是 VMware/VirtualBox**：请先将虚拟机关机，进入虚拟机设置，添加 **2 块** 新的虚拟硬盘（容量建议 5GB 即可，用于实验）。添加完成后开机。
*   **如果是云服务器**：在云控制台购买并挂载两块云盘。

**2. 确认新硬盘是否被系统识别**
执行以下命令查看当前系统的块设备：
```bash
lsblk
```
*预期输出*：你应该能看到原来的系统盘 `/dev/sda`，以及新增加的 `/dev/sdb` 和 `/dev/sdc`。**切记：后续操作绝对不要碰 `/dev/sda`！**

**3. 安装 RAID 管理工具**
CentOS 7 默认可能没有安装 `mdadm`，需要先安装：
```bash
sudo yum install -y mdadm
```

---

### 🚀 第二阶段：创建与配置 RAID 0

**1. 创建 RAID 0 阵列**
我们使用 `/dev/sdb` 和 `/dev/sdc` 两块盘创建一个名为 `/dev/md0` 的 RAID 0 设备。
```bash
sudo mdadm --create /dev/md0 --level=0 --raid-devices=2 /dev/sdb /dev/sdc
```
*参数解释*：
*   `--create /dev/md0`：创建并命名为 md0。
*   `--level=0`：指定 RAID 级别为 0（条带化）。
*   `--raid-devices=2`：指定使用两块活动磁盘。
*   *执行时如果提示是否继续，输入 `y` 确认。*

**2. 查看 RAID 创建状态**
```bash
cat /proc/mdstat
```
*预期输出*：你会看到 `md0 : active raid0 sdc[1] sdb[0]`，且容量是两块盘的总和。刚创建时，底部的 `[=>...]` 进度条可能还在 `resync`（同步），等待其变为 `[UU]` 即代表就绪。

**3. 格式化 RAID 0 设备**
使用 XFS 文件系统进行格式化（CentOS 7 默认推荐）：
```bash
sudo mkfs.xfs /dev/md0
```

**4. 挂载并使用**
创建一个挂载点，将 RAID 0 挂载上去：
```bash
sudo mkdir -p /mnt/raid0
sudo mount /dev/md0 /mnt/raid0
# 查看挂载情况和容量
df -h
```
*你会发现 `/mnt/raid0` 的容量接近两块盘的总和（例如 10GB）。*

---

### 💾 第三阶段：配置持久化（重启不丢失）

为了防止重启后 RAID 阵列和挂载丢失，需要写入配置文件。

**1. 保存 RAID 配置**
```bash
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm.conf
```

**2. 配置开机自动挂载**
首先获取 `/dev/md0` 的 UUID：
```bash
sudo blkid /dev/md0
```
*复制输出中的 `UUID="xxxx-xxxx-xxxx"` 部分。*

然后编辑 `/etc/fstab` 文件：
```bash
sudo vi /etc/fstab
```
在文件末尾添加一行（请替换为你自己的 UUID）：
```text
UUID=你的UUID /mnt/raid0 xfs defaults 0 0
```

---

### 🔍 第四阶段：测试与清理（实验结束）

**1. 测试读写**
```bash
# 写入一个测试文件
sudo dd if=/dev/zero of=/mnt/raid0/test.img bs=1M count=100
# 查看文件
ls -lh /mnt/raid0/
```

**2. 🚨 如何彻底销毁实验环境（排错/重置用）**
如果你想把这两块盘恢复成普通的裸盘，必须按以下顺序清理：
```bash
# 1. 卸载挂载点
sudo umount /mnt/raid0

# 2. 停止 RAID 设备
sudo mdadm --stop /dev/md0

# 3. 清除硬盘上的 RAID 超级块（非常重要，否则盘会一直被识别为 RAID 成员）
sudo mdadm --zero-superblock /dev/sdb
sudo mdadm --zero-superblock /dev/sdc

# 4. 清理配置文件（可选）
sudo vi /etc/mdadm.conf  # 删除对应行
sudo vi /etc/fstab       # 删除对应行
```

**⚠️ 运维提醒**：在实验过程中，你可以随意把 `/dev/sdb` 或 `/dev/sdc` 移除（在虚拟机设置中），重新启动后你会发现 RAID 0 直接崩溃无法挂载。这就是因为 RAID 0 **没有任何容错能力**，坏一块盘，整个数据全毁。这也是为什么生产环境很少用 RAID 0 存核心数据的原因。