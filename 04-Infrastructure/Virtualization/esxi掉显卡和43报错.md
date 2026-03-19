## 显卡报错43

应该是windows更新的原因

# 掉显卡

问题描述：虚拟机正常运行但是显卡会自动掉线

查看vmkernel.log

```
2026-02-07T09:29:18.734Z In(182) vmkernel: cpu59:2099897)PCI: 1401: Skipping device reset on 0000:05:00.0 because PCIe link to the device is down.
2026-02-07T09:29:18.734Z In(182) vmkernel: cpu59:2099897)PCIPassthru: 1750: Disable Domain for device 0000:05:00.0
2026-02-07T09:29:18.734Z In(182) vmkernel: cpu59:2099897)PCIPassthru: 1039: pcipdevInfo: 0x4314737b68e0 (0000:05:00.0), state 0, destroyed
```

## 问题推测

显卡进入省电模式

## 修改bios中的pcie设置

![](assets/esxi掉显卡和43报错/file-20260208154000678.png)

![](assets/esxi掉显卡和43报错/file-20260208153930885.png)

```

PCI Devices Option ROM Setting

将所有的Legacy换成EFI

```


## Linux系统设置

- **开启持久化模式**：`sudo nvidia-smi -pm 1`。
    
- **锁定高性能频率**：`sudo nvidia-smi -lgc 1000,2500`。
    
- **禁用内核 ASPM**：
    
    - 编辑 `/etc/default/grub`。
        
    - 修改行：`GRUB_CMDLINE_LINUX_DEFAULT="quiet splash pcie_aspm=off"`。
        
    - 运行 `sudo update-grub` 并重启。

## ESXI系统设置

连接ESXI的ssh客户端

1.  **禁用 ESXi 的 PCIe Hot-Plug 功能**：`esxcli system settings kernel set -s "enablePCIEHotplug" -v "FALSE"`
2. **修改 `/etc/vmware/passthru.map` 的 passthrough quirk 映射**
	# NVIDIA (FLR issue on Ampere and Hopper GPUs) 10de ffff bridge false
	改为：
	NVIDIA (FLR issue on Ampere and Hopper GPUs) 10de ffff default false

