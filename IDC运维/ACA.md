## 1

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20261107164657827_2026-11-07_164657.png)

#### 题目解析：为什么选 D（32GB/s），而 C（16GB/s）是错的？

这道题的核心陷阱在于 **PCIe 是“全双工”总线**，同时默认考察的是 **PCIe 3.0 或 4.0** 的主流标准。选择 16GB/s 是因为只计算了**单向带宽**，而忽略了双向传输能力。

#### 1. 为什么 C（16GB/s）是个“美丽”的陷阱？
如果你按 **PCIe 3.0 x16** 的**单向**带宽来计算，公式如下：
*   **单 Lane 速率**：8 GT/s
*   **编码效率**：128b/130b（约 98.5%）
*   **计算**：8 GT/s × (128/130) × 16（Lane数） ÷ 8（bit转Byte） ≈ **15.75 GB/s**（通常近似记为 **16GB/s**）。

这就是你选 C 的原因，你算出了数据从 CPU 发往设备（或从设备发往 CPU）的**单向理论极限**。

#### 2. 为什么正确答案是 D（32GB/s）？
因为 **PCIe 是全双工（Full Duplex）总线**。就像双向车道，发送和接收通道是物理独立的，两边可以同时通车。
*   在计算“最高传输带宽”时，行业惯例和考试标准通常指**双向总带宽（即总吞吐量）**。
*   既然单向是 16GB/s，那么双向同时传输就是：16GB/s × 2 = **32GB/s**（精确值约为 31.5GB/s，取整即 32GB/s）。

**另一种理解可能（按 PCIe 4.0 计算）：**
如果是近年来的考试，可能会默认考察 **PCIe 4.0 x16 的单向带宽**：
*   PCIe 4.0 单 Lane 速率为 16 GT/s，是 3.0 的两倍。
*   单向带宽：16 GT/s × (128/130) × 16 ÷ 8 ≈ **31.5 GB/s**（近似为 **32GB/s**）。

#### 💡 总结记忆法
*   遇到这类未指明代际的题，如果选项中有 `16` 和 `32`，大概率是在考 **PCIe 3.0 x16**。
*   记住：**16GB/s 是单向（只发或只收），32GB/s 是全双工双向（同时发和收）**。题干问“最高传输带宽”，全双工总线必须算双向总吞吐量。

## 2

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20261107165045152_2026-11-07_165045.png)

虚拟交换机是软件定义的

## 3

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20261107165128457_2026-11-07_165128.png)


## 3

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20261107165404105_2026-11-07_165404.png)

三层交换机可以工作在网络层

## 4

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20261107165512426_2026-11-07_165512.png)

**A. IEEE802.1w下所有VLAN使用一棵生成树**

- **解析**：IEEE 802.1w 是 **RSTP（快速生成树协议）**。它的核心改进是**加快了网络拓扑变化的收敛速度**（从传统的几十秒缩短到几秒甚至毫秒级）。
    
- 但是，RSTP 在实例划分上继承了最早的 IEEE 802.1D（传统 STP），它**依然是单实例的**。也就是说，无论你的交换机上划分了多少个 VLAN，它们**全部共用这一棵生成树**。因此，A 的描述是完全正确的。

## 5

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20261107182244881_2026-11-07_182244.png)

**D. CLOS架构的网络是云计算数据中心网络发展的终极形态**

- **解析**：这句话错在**“终极形态”**四个字。在计算机和网络技术领域，几乎不存在“终极形态”。技术永远是随着业务需求（如AI大模型、算力网络、东数西算）在不断演进的。虽然目前 Spine-Leaf（基于CLOS架构）是云数据中心的主流甚至统治性架构，但未来依然可能出现新的拓扑（如全光交换网络、AI专属网络拓扑等）。**凡是带“终极”“绝对”“完美”等字眼的选项，在选择题中大概率是错的。**

## 6

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20261107182406211_2026-11-07_182406.png)
**NVDIMM = Non-Volatile Dual In-line Memory Module**，中文通常译为“**非易失性双列直插内存模组**”

## 7

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20261107182754645_2026-11-07_182754.png)

## 8

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20261107182830048_2026-11-07_182830.png)
