

## 禁用nouveau驱动

```bash

cat >> /etc/modprobe.d/blacklist-nouveau.conf << EOF
blacklist nouveau
blacklist lbm-nouveau
options nouveau modeset=0
EOF

```

## 下载run文件

`https://download.nvidia.com/XFree86/Linux-x86_64/470.129.06/NVIDIA-Linux-x86_64-470.129.06.run`

```bash

wget https://download.nvidia.com/XFree86/Linux-x86_64/470.129.06/NVIDIA-Linux-x86_64-470.129.06.run

chmod +x /home/hbyb/NV

./NV

```

