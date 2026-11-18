你好！我是你的 Linux 命令行讲师。`nmcli` 是 NetworkManager 的命令行工具，用来**持久化地**管理网络连接。和 `[[ip命令]]` 的临时配置不同，`nmcli` 改的配置写进配置文件、重启不丢——这正是生产环境改网络该用的方式。

---

### A. 一句话定义和适用场景

**`nmcli`（NetworkManager command-line interface）是管理 NetworkManager 网络服务的命令行工具，可创建、修改、启停网络连接并持久化保存配置。**

**适用场景**：RHEL/CentOS 7 及以后、主流发行版默认用 NetworkManager 管网。要给服务器配静态 IP、绑定网卡（bond/team）、配网桥（给 KVM 虚拟机用）、永久改 DNS，都用 `nmcli`。它比手改 ifcfg 配置文件更不容易出错。

---

### B. 最常用的 6 个命令形式

#### 1. `nmcli device status`（或 `nmcli d`）【只读/安全】
*   **作用**：查看所有网络设备及其连接状态。
*   **参数解释**：看哪块网卡 connected（已连）、disconnected（未连）、unmanaged（不受 NM 管理）。排查第一步。

#### 2. `nmcli connection show`（或 `nmcli c`）【只读/安全】
*   **作用**：列出所有已保存的网络连接配置（profile）。
*   **参数解释**：注意"连接（connection）"是配置，"设备（device）"是硬件，一个设备可有多个连接配置。

#### 3. `nmcli connection show <连接名>` 【只读/安全】
*   **作用**：查看某个连接的详细配置（IP、网关、DNS、_method）。
*   **参数解释**：改配置前先看清楚现状。

#### 4. `nmcli con mod <连接名> ipv4.addresses 10.0.0.11/24` 【会改配置/谨慎】
*   **作用**：修改连接的静态 IP 地址。
*   **参数解释**：常配合一整组参数：`ipv4.method manual`（设为手动/静态）、`ipv4.gateway`、`ipv4.dns`。**改完要 `nmcli con up` 或重启连接才生效**。

#### 5. `nmcli con up <连接名>` / `nmcli con down <连接名>` 【会改状态/谨慎】
*   **作用**：启用/停用某个网络连接。
*   **参数解释**：改完配置后用 `up` 让它生效。远程操作 down 掉当前连接 = 断网失联，务必小心。

#### 6. `nmcli con add type bond ...` 【会改配置/高级】
*   **作用**：创建 bond（网卡绑定）、bridge（网桥）、vlan 等高级连接。
*   **参数解释**：IDC 常做双网卡 bond 做冗余/负载均衡，这是生产环境的重点应用。

---

### C. "只读安全"示例：配置静态 IP 的标准流程

假设要给 `ens1f0` 配静态 IP `10.0.0.11/24`、网关 `10.0.0.1`、DNS `8.8.8.8`：

```bash
# 1. 先看设备和连接
nmcli device status
nmcli connection show

# 2. 修改连接为静态 IP（假设连接名叫 ens1f0）
nmcli con mod ens1f0 ipv4.method manual \
    ipv4.addresses 10.0.0.11/24 \
    ipv4.gateway 10.0.0.1 \
    ipv4.dns "8.8.8.8 114.114.114.114" \
    connection.autoconnect yes

# 3. 重启连接使其生效
nmcli con down ens1f0 && nmcli con up ens1f0

# 4. 验证
ip addr show ens1f0
ip route
```

**关键点解析**：
*   **`ipv4.method manual`**：必须设成 manual（静态）才会用你指定的地址；默认是 auto（DHCP）。
*   **`connection.autoconnect yes`**：开机自动连接，生产服务器必须设为 yes。
*   **DNS 用引号、多个空格分隔**：写进配置文件的多个 DNS 服务器。
*   **配置持久化**：这些改动写进 `/etc/sysconfig/network-scripts/`（RHEL 系）或 Netplan（Ubuntu），**重启不丢**——这是和 `ip addr add` 的本质区别。

---

### D. IDC 运维高危大坑（远程改网络=高风险操作）

**场景：SSH 远程改 IP 把自己锁在外面**
这是运维经典事故。`nmcli con mod` 改完 IP，`con down && con up` 的瞬间，旧 SSH 连接断开；如果新 IP 配错（掩码/网关错误），你就再也连不上了。

**必须遵守的安全流程**：
1. **留后路**：确认能通过 `[[ipmitool]]`（iDRAC/iLO/BMC）的 KVM 或 SOL 访问，万一失联还能救。
2. **设定时回退**：改之前用 `at` 或 `nohup sleep 300; nmcli con mod ... 恢复原配置` 设一个"5 分钟后自动恢复"，确认新配置 OK 后取消它。
3. **一次只改一块网卡**：多网卡机器不要同时 down 所有连接。
4. **走变更窗口**：遵循 `[[SOP]]`、`[[运维红线]]`，双人复核。

---

### E. 常见误区与排错思路

1.  **误区：`nmcli` 和 `ip` 改的 IP 混用**
    *   **纠正**：`ip addr add` 是临时的（重启丢），`nmcli` 是持久的。生产环境用 `nmcli`；`ip` 只用于临时调试/排障。
2.  **误区：Device 和 Connection 搞混**
    *   **纠正**：`device` 是物理网卡，`connection` 是配置模板。一个 device 可以有多个 connection（比如一个静态、一个 DHCP），但同一时刻只有一个 active。改配置是针对 connection，看状态看 device。
3.  **误区：改完配置立即生效**
    *   **纠正**：`nmcli con mod` 只是改配置存盘，**要 `nmcli con up` 重新激活才生效**。这是新手最常踩的坑——改完发现 IP 没变，就是忘了 up。
4.  **误区：NetworkManager 没运行**
    *   **排错**：`systemctl status NetworkManager` 确认服务在跑。有些最小化安装的服务器 NM 没启用，此时 `nmcli` 会报"not running"，得先启动或用配置文件方式。

---

### F. 小练习（自己动手试试）

1.  **（基础）** 执行 `nmcli device status` 和 `nmcli connection show`，理解 device 和 connection 的区别。
2.  **（进阶）** 挑一个不重要的连接，执行 `nmcli con show <连接名>`，找到它的 `ipv4.method` 和 `ipv4.addresses`。
3.  **（排错）** 执行 `systemctl status NetworkManager`，确认服务状态，思考如果它没运行 `nmcli` 会怎样。

---

### G. 是否继续深入？

`nmcli` 做持久化网络管理，接下来你可以：
*   用 `[[ip命令]]` 做临时网络排查；
*   用 `[[ethtool]]` 查网卡硬件层；
*   或深挖"双网卡 bond 配置"和"KVM 网桥配置"这两个 IDC 高频实战。告诉我！
