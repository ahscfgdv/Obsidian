
问题描述

查看vmkernel.log

```
2026-02-07T09:29:18.734Z In(182) vmkernel: cpu59:2099897)PCI: 1401: Skipping device reset on 0000:05:00.0 because PCIe link to the device is down.
2026-02-07T09:29:18.734Z In(182) vmkernel: cpu59:2099897)PCIPassthru: 1750: Disable Domain for device 0000:05:00.0
2026-02-07T09:29:18.734Z In(182) vmkernel: cpu59:2099897)PCIPassthru: 1039: pcipdevInfo: 0x4314737b68e0 (0000:05:00.0), state 0, destroyed
```

修改bios中的pcie设置

![](assets/esxi掉显卡和43报错/file-20260208154000678.png)

![](assets/esxi掉显卡和43报错/file-20260208153930885.png)

```

PCI Devices Option ROM Setting

将所有的Legacy换成EFI


```



cat /var/log/vmkernel.log | grep "0000:05:00.0"

