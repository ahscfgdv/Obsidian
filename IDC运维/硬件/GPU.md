dcgm



nvidia-smi（NVIDIA System Management Interface）是随NVIDIA驱动一同安装的命令行工具，用于监控和管理服务器上的NVIDIA GPU。对IDC运维而言，它的核心价值在于**快速判断GPU是否在健康地工作**——“利用率100%”不等于“健康”，降频、ECC计数增长、PCIe链路缩水都不会直接报错，但nvidia-smi能看到。


#### 最基础的用法：直接运行

不加任何参数，输出一个概览表，包含每张卡的型号、温度、功耗、显存、利用率和进程信息。

```bash
nvidia-smi
```

**概览输出中需要重点关注的信息**：

- **Perf（性能状态）** ：P0为最高性能，P12为最低。如果业务负载很高但Perf显示在P2以下，说明GPU正在被限频。
- **Pwr:Usage/Cap**：实际功耗/功耗上限。Usage长期贴着Cap，意味着GPU在撞功耗墙。
- **GPU-Util**：计算单元利用率。高利用率本身不代表健康，一张被降频的卡利用率照样可以显示100%。
- **Memory-Usage**：已用/总显存。接近上限时需警惕OOM。
- **Uncorr. ECC**：不可纠正的ECC错误。出现非零值是需要立即关注的红旗。


#### 实时动态监控

概览是静态的，排查问题往往需要持续观察。

**方法一：配合watch命令**

```bash
watch -n 2 nvidia-smi
```

每2秒刷新一次，最直观的持续观察方式。

**方法二：nvidia-smi自身的循环参数**

```bash
nvidia-smi -l 2
```

`-l`（或`--loop`）直接让nvidia-smi每2秒刷新一次，效果与watch类似，但输出更“干净”。

**方法三：持续写入日志**

```bash
nvidia-smi -i 0 -l 2 -f gpu_log.txt
```

监控0号GPU，每2秒刷新，结果写入文件。适合分布式训练中事后定位通信瓶颈或性能拐点。


#### 只查看指定GPU

多卡服务器上，先确定要操作的是哪一张。

```bash
# 列出所有GPU及其UUID
nvidia-smi -L

# 只对0号GPU执行后续操作
nvidia-smi -i 0
```

`-i`可以跟序号、UUID或PCI总线地址。**运维场景推荐使用UUID**，因为序号在重启后可能变化。


#### 详细查询：按模块筛选

`-q`输出非常长的详细信息，配合`-d`可以只显示关心的模块，避免信息过载。

```bash
# 只查温度
nvidia-smi -q -d TEMPERATURE

# 只查功耗
nvidia-smi -q -d POWER

# 只查ECC错误计数
nvidia-smi -q -d ECC

# 同时查温度和功耗
nvidia-smi -q -d TEMPERATURE,POWER

# 只查指定GPU的显存详情
nvidia-smi -i 0 -q -d MEMORY
```

**温度阈值参考**：多数GPU持续高于85℃即存在降频风险；HBM显存在75℃以上时错误率会显著上升。


#### 定制化查询：适合脚本和监控采集

`--query-gpu`可以精确指定要输出的字段，配合`--format=csv`输出CSV，便于导入监控系统或做日志分析。

```bash
nvidia-smi --query-gpu=index,name,temperature.gpu,power.draw,utilization.gpu,memory.used,memory.total,ecc.errors.uncorrected.aggregate.total --format=csv
```

常用字段包括：`index`、`name`、`temperature.gpu`、`power.draw`、`utilization.gpu`、`memory.used`、`memory.total`、`pstate`、`ecc.errors.uncorrected.aggregate.total`。


#### 故障排查：最常用的几个命令

GPU的问题很少以“彻底坏掉”的形式出现，更常见的是悄悄降速、错误累积、链路缩水。以下是排查时最直接的手段。

**检查ECC错误**

```bash
nvidia-smi -q -d ECC
```

关注`Volatile`和`Aggregate`下的`Uncorrectable`计数。**可纠正ECC（Correctable）持续增长不是噪声，是显存单元老化的证据**，应纳入监控并关注增量趋势。

**检查PCIe链路状态**

```bash
nvidia-smi -q | grep -A 10 "PCIe"
```

重点看`Link Width`和`Link Gen`。如果一张Gen4 x16的卡显示为Gen1或x8，带宽会大幅缩水，模型加载和跨卡通信都会变慢。

**查看XID错误（需要内核日志）**

nvidia-smi本身不能直接列出XID历史，XID是驱动写入内核日志的事件码，需要配合`dmesg`：

```bash
dmesg | grep -i "Xid"
```

常见的XID 79（GPU has fallen off the bus）意味着掉卡，XID 48与ECC错误相关，XID 54可能指向供电线缆未插好。

**NVLink状态（多卡训练场景）**

```bash
nvidia-smi nvlink --status
```

如果NVLink链路显示`inactive`，多卡之间的高速互联已失效，分布式训练的性能会严重受损。

**进程级排查**

```bash
nvidia-smi -q -d PROCESSES
```

查看每张卡上运行了哪些进程及其显存占用，用于定位显存泄漏或异常占用。


#### 管理操作（需root权限）

以下操作会改变GPU状态，需谨慎使用。

**功耗限制调整**

```bash
nvidia-smi -i 0 -pl 200
```

将0号GPU的功耗上限设为200W。在推理场景中，适当降压功耗可以在精度不变的前提下降低TCO，但需确认业务负载能接受相应的性能变化。

**计算模式切换**

```bash
nvidia-smi -i 0 -c 1   # 设置为EXCLUSIVE_PROCESS（独占进程模式）
nvidia-smi -i 0 -c 0   # 恢复为DEFAULT模式
```

独占模式下，该GPU同一时间只允许一个进程访问，适合需要稳定资源隔离的场景，但会阻止其他进程使用该卡。

**持久模式**

```bash
nvidia-smi -i 0 -pm 1
```

开启后GPU驱动常驻，减少新应用启动时的初始化时间，代价是待机功耗略有增加。


#### 速查清单

| 场景 | 命令 | 关注点 |
| :--- | :--- | :--- |
| 快速看整体状态 | `nvidia-smi` | Perf、Pwr Usage/Cap、GPU-Util、显存 |
| 持续观察 | `watch -n 2 nvidia-smi` 或 `nvidia-smi -l 2` | 动态变化，而非单次快照 |
| 只盯一张卡 | `nvidia-smi -i 0` | 先用`-L`确认序号或UUID |
| 查温度 | `nvidia-smi -q -d TEMPERATURE` | 持续高于85℃需关注 |
| 查ECC | `nvidia-smi -q -d ECC` | 不可纠正错误非零；可纠正错误看增量趋势 |
| 查PCIe链路 | `nvidia-smi -q \| grep -A 10 "PCIe"` | Link Width和Link Gen是否达标 |
| 查XID | `dmesg \| grep -i "Xid"` | XID 79掉卡、XID 48 ECC相关 |
| 查NVLink | `nvidia-smi nvlink --status` | 多卡场景关注inactive链路 |
| 脚本采集 | `--query-gpu=... --format=csv` | 指定字段，便于解析 |
| 功耗限制 | `nvidia-smi -i 0 -pl 200` | 需root，确认业务可接受性能变化 |

**一句话总结**：nvidia-smi的日常用法可以归纳为“**先看概览定状态，再看Perf和ECC判断健康度，持续监控用`-l`，故障排查查ECC/PCIe/XID/NVLink，脚本采集用`--query-gpu`**”；利用率100%不是健康指标，Perf降级、ECC增长、PCIe链路缩水才是需要优先处置的信号。