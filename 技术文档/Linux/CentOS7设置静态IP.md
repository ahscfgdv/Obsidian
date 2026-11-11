#### 准备工作：确认网卡名称

在 CentOS 7 中，网卡名称通常不再是传统的 `eth0`，而是类似 `ens33`、`ens192` 或 `eno16777736` 这种命名格式。在修改配置前，必须先确认你要修改哪块网卡：

```bash
ip addr
```
或者
```bash
nmcli device status
```
找到你需要配置静态 IP 的网卡名称，例如 `ens33`。

#### 第一步：修改网卡配置文件

CentOS 7 的网卡配置文件位于 `/etc/sysconfig/network-scripts/` 目录下，文件名格式为 `ifcfg-<网卡名>`。

使用 `vi` 或 `vim` 编辑器打开配置文件（需要 root 权限）：
```bash
sudo vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

文件中原有的内容可能是动态获取（DHCP）的，你需要将其修改或添加为以下关键参数。**请根据你的实际网络环境（如虚拟机 NAT 模式、桥接模式或物理机 IDC 环境）替换对应的 IP、网关和 DNS**：

```ini
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
# 核心修改1：将 dhcp 改为 static（或 none），表示使用静态 IP
BOOTPROTO=static 
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  # 保持原有不变
DEVICE=ens33
# 核心修改2：确保开机自启为 yes
ONBOOT=yes

# 核心添加：配置静态 IP 地址、子网掩码、网关和 DNS
IPADDR=192.168.10.100        # 你想要设置的静态 IP
PREFIX=24                    # 子网掩码长度（24 等同于 255.255.255.0）
GATEWAY=192.168.10.2         # 网关地址（虚拟机 NAT 模式下通常是 .2 结尾）
DNS1=8.8.8.8                 # 首选 DNS
DNS2=114.114.114.114         # 备用 DNS
```

**⚠️ 注意事项**：
*   **`PREFIX=24`** 对应 `NETMASK=255.255.255.0`，两者写一个即可。
*   **`IPADDR`** 必须与 `GATEWAY` 在同一个网段。
*   **`DNS`** 也可以配置在 `/etc/resolv.conf` 中，但写在网卡配置里能保证重启网络后不丢失。

#### 第二步：重启网络服务使配置生效

修改保存后，需要重启网络服务来应用新的配置。CentOS 7 推荐使用以下命令：

```bash
sudo systemctl restart network
```

如果系统提示 `Restarting network (via systemctl): Job for network.service failed...`，说明配置文件有语法错误，或者有冲突的 NetworkManager 服务在干预。

**备选方案（如果上述命令报错）**：
```bash
sudo nmcli connection reload
sudo nmcli connection up ens33
```

#### 第三步：验证配置是否成功

1.  **查看 IP 是否已修改**：
    ```bash
    ip addr show ens33
    ```
    确认输出的 `inet` 后面是你设置的静态 IP。

2.  **测试网关连通性**：
    ```bash
    ping -c 4 192.168.10.2  # 换成你配置的网关
    ```

3.  **测试外网连通性和 DNS 解析**：
    ```bash
    ping -c 4 8.8.8.8       # 测试纯 IP 外网连通性
    ping -c 4 www.baidu.com # 测试 DNS 解析是否正常
    ```

#### 第四步：常见排错思路

*   **问题：Ping 不通网关**
    *   检查虚拟机的网络模式（如 VMware 的 NAT 模式网段通常是 `192.168.x.x`，网关通常为 `192.168.x.2`，需核对虚拟网络编辑器）。
    *   检查物理机/交换机端对应 VLAN 是否正确放行。
*   **问题：能 Ping 通 IP，但 Ping 不通域名**
    *   这是 DNS 未配置或配置错误。检查 `/etc/resolv.conf` 文件，或确认网卡配置里的 `DNS1` 是否正确。
*   **问题：NetworkManager 服务冲突**
    *   CentOS 7 默认使用 NetworkManager 管理网络。如果你手动修改了配置文件但未生效，可以尝试关闭传统的 `network` 服务，完全交由 NetworkManager 接管：
        ```bash
        sudo systemctl stop network
        sudo systemctl disable network
        sudo systemctl restart NetworkManager
        ```
*   **问题：MAC 地址冲突或绑定错误**
	    *   如果是克隆的虚拟机，网卡配置文件中的 `UUID` 或 `HWADDR` 可能与新机器不匹配。删除 `UUID` 和 `HWADDR` 行，重启网络服务即可重新生成。