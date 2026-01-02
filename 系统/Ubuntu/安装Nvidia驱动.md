

## 禁用nouveau驱动

```bash

cat >> /etc/modprobe.d/blacklist-nouveau.conf << EOF
blacklist nouveau
blacklist lbm-nouveau
options nouveau modeset=0
EOF

```