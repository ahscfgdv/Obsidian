你好！我是你的 Linux 命令行讲师。`lscpu` 是查看 CPU 信息的"身份证阅读器"。面试时被问到"一台服务器多少路、多少核、支不支持超线程"，或者要评估虚拟化超分比、做 NUMA 绑核，第一个就该想到它。

---

### A. 一句话定义和适用场景

**`lscpu`（list CPU）是一个显示 CPU 架构、型号、核心数、线程数、插槽数、NUMA 拓扑、缓存等信息的只读命令。**

**适用场景**：机器到货验收核对配置（是不是采购的 2 路 32 核）、判断能否跑虚拟化、排查"为什么 32 核的机器 top 里只看到 16 核"、做数据库 NUMA 优化时，都用 `lscpu`。

---

### B. 最常用的 5 个命令形式

#### 1. `lscpu` 【只读/安全】
*   **作用**：以整齐的表格输出 CPU 的全套关键信息。
*   **参数解释**：无参数直接运行，信息来自 `/proc/cpuinfo` 和 sysfs，无需 root。

```shell
[root@localhost ~]# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                1
On-line CPU(s) list:   0
Thread(s) per core:    1
Core(s) per socket:    1
Socket(s):             1
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 165
Model name:            Intel(R) Core(TM) i7-10750H CPU @ 2.60GHz
Stepping:              2
CPU MHz:               2592.008
BogoMIPS:              5184.01
Hypervisor vendor:     VMware
Virtualization type:   full
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              12288K
NUMA node0 CPU(s):     0
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon nopl xtopology tsc_reliable nonstop_tsc eagerfpu pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch invpcid_single ssbd ibrs ibpb stibp fsgsbase tsc_adjust bmi1 avx2 smep bmi2 invpcid rdseed adx smap clflushopt xsaveopt xsavec xgetbv1 arat md_clear spec_ctrl intel_stibp flush_l1d arch_capabilities

```

#### 2. `lscpu -e` 【只读/安全】
*   **作用**：以"每个 CPU 核心一行"的扩展表格显示 NUMA 节点、核心、插槽、线程的对应关系。
*   **参数解释**：`-e` = extend。做绑核（`taskset`）、看哪几个核在同一个 NUMA 节点时用。

#### 3. `lscpu | grep -E 'Model name|CPU\(s\)|Socket|Core|Thread|NUMA|MHz'` 【只读/安全】
*   **作用**：只挑出最关心的几行，快速核对配置。
*   **参数解释**：正则一次筛多个关键词，脚本和日常核对很常用。

#### 4. `lscpu -C` 【只读/安全】
*   **作用**：只显示各级缓存（L1d/L1i/L2/L3）大小。
*   **参数解释**：`-C` = cache。核对 CPU 缓存规格，呼应 `[[CPU#缓存]]`。

#### 5. `lscpu --all` 【只读/安全】
*   **作用**：显示所有 CPU（包括当前离线 offline 的核）。
*   **参数解释**：排查"为什么少了几核"——有时核心被系统离线了，`lscpu` 默认只显示在线的，`--all` 能看到全貌。

---

### C. "只读安全"示例：读懂输出

执行 `lscpu`（节选关键行）：

```text
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                64
On-line CPU(s) list:   0-63
Thread(s) per core:    2
Core(s) per socket:    16
Socket(s):             2
NUMA node(s):          2
Vendor ID:             GenuineIntel
Model name:            Intel(R) Xeon(R) Gold 6338 CPU @ 2.00GHz
CPU MHz:               2000.000
CPU max MHz:           3200.0000
L3 cache:              32768K
NUMA node0 CPU(s):     0-15,32-47
NUMA node1 CPU(s):     16-31,48-63
```

**字段解析（这几个面试必会）**：
*   **`CPU(s): 64`**：逻辑 CPU 总数 = 插槽 × 每路核心 × 每核线程 = 2 × 16 × 2 = 64。
*   **`Socket(s): 2`**：**2 路**（物理 CPU 有 2 颗）。这就是"双路服务器"。
*   **`Core(s) per socket: 16`**：每颗 CPU 16 个物理核。
*   **`Thread(s) per core: 2`**：每核 2 线程，说明**开启了超线程（HT）**。若为 1 则是关了超线程。
*   **`NUMA node(s): 2`**：2 个 NUMA 节点，通常**一颗 CPU 一个节点**，本地内存访问快、跨节点慢。
*   **`Model name`**：具体型号，这里可对回 `[[CPU]]` 里的 Intel Xeon Gold 6338。

**💡 计算公式**：物理核总数 = Socket(s) × Core(s) per socket = 2 × 16 = **32 核**；逻辑核 = 32 × 2 = **64 线程**。面试答"几路几核"要给物理核，别把超线程算进去。

---

### D. IDC 运维高危大坑（lscpu 只读，但数据要用对）

`lscpu` 零风险，但有个反复被问的坑：

**场景**：采购单写"32 核"，`lscpu` 的 `CPU(s)` 却显示 64，有人以为厂商多给了核。
**真相**：`CPU(s)` 是**逻辑核**，开了超线程自然翻倍。要回答"物理几核"，用 `Socket × Core per socket`，或 `grep -c 'core id' /proc/cpuinfo | sort -u`。

**虚拟化超分的坑**：在 KVM/ESXi 宿主机上，`lscpu` 看到的是宿主机暴露给虚拟机的 vCPU，不一定是物理核。评估能否超分时，要清楚"宿主机物理核 → 分配给虚拟机的 vCPU"的倍数关系（比如 1 核分 4 个 vCPU 就是 4 倍超分），超分过高会导致 CPU 就绪队列排队、业务卡顿。

---

### E. 常见误区与排错思路

1.  **误区：把逻辑核当物理核**
    *   **纠正**：记牢 `逻辑核 = Socket × Core × Thread`。面试回答配置时，先说物理路数、物理核数，再补一句是否开超线程。
2.  **误区：`CPU(s)` 比预期少**
    *   **排错**：① `lscpu --all` 看是否有离线的核；② 查 BIOS 是否禁用了部分核心；③ 虚拟机可能被限制了 vCPU 数量；④ 老系统内核可能没开多核支持。
3.  **误区：忽略 NUMA**
    *   **纠正**：多路服务器上，跨 NUMA 节点访问内存延迟高。数据库、Java 大堆应用要考虑 NUMA 绑核绑内存（`numactl`）。NUMA 与内存通道的关系见 `[[CPU#CPU内存通道]]`。

---

### F. 小练习（自己动手试试）

1.  **（基础）** 执行 `lscpu`，算出你这台机器的物理核总数和逻辑核总数，并判断是否开启超线程。
2.  **（进阶）** 执行 `lscpu -e`，观察 `NODE`、`CPU`、`CORE`、`SOCKET` 四列的对应关系，理解同一物理核的两个超线程是如何编号的。
3.  **（排错）** 对比 `lscpu | grep '^CPU(s)'` 和 `lscpu --all | grep -c '^CPU'`，看看在线核和总核是否一致。

---

### G. 是否继续深入？

`lscpu` 看 CPU，接下来你可以：
*   用 `[[lspci]]` 看 CPU 之外的 PCI 设备；
*   用 `[[dmidecode]]` 读 CPU 的序列号和完整规格；
*   或深挖 NUMA 绑核、超线程对业务的影响。告诉我！
