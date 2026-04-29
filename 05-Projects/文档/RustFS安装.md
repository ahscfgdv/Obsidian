## 安装部署

### Linux脚本安装**SNSD(单机单盘)** 模式

```bash

curl -O https://rustfs.com/install_rustfs.sh

chmod +x install_rustfs.sh

bash ./install_rustfs.sh

# 根据提示一步步操作

```

### Linux手动安装**SNSD(单机单盘)** 模式

```bash

wget https://dl.rustfs.com/artifacts/rustfs/release/rustfs-linux-x86_64-musl-latest.zip
unzip rustfs-linux-x86_64-musl-latest.zip 
chmod +x rustfs 
mv rustfs /usr/local/bin/

# 单机单盘模式
sudo tee /etc/default/rustfs <<EOF
RUSTFS_ACCESS_KEY=rustfsadmin
RUSTFS_SECRET_KEY=rustfsadmin
RUSTFS_VOLUMES="/data/rustfs0"
RUSTFS_ADDRESS=":9000"
RUSTFS_CONSOLE_ENABLE=true
RUST_LOG=error
RUSTFS_OBS_LOG_DIRECTORY="/var/logs/rustfs/"
EOF

sudo mkdir -p /data/rustfs0 /var/logs/rustfs /opt/tls
sudo chmod -R 750 /data/rustfs* /var/logs/rustfs

sudo tee /etc/systemd/system/rustfs.service <<EOF
[Unit]
Description=RustFS Object Storage Server
Documentation=https://rustfs.com/docs/
After=network-online.target
Wants=network-online.target

[Service]
Type=notify
NotifyAccess=main
User=root
Group=root

WorkingDirectory=/usr/local
EnvironmentFile=-/etc/default/rustfs
ExecStart=/usr/local/bin/rustfs \$RUSTFS_VOLUMES

LimitNOFILE=1048576
LimitNPROC=32768
TasksMax=infinity

Restart=always
RestartSec=10s

OOMScoreAdjust=-1000
SendSIGKILL=no

TimeoutStartSec=30s
TimeoutStopSec=30s

NoNewPrivileges=true
 
ProtectHome=true
PrivateTmp=true
PrivateDevices=true
ProtectClock=true
ProtectKernelTunables=true
ProtectKernelModules=true
ProtectControlGroups=true
RestrictSUIDSGID=true
RestrictRealtime=true

# service log configuration
StandardOutput=append:/var/logs/rustfs/rustfs.log
StandardError=append:/var/logs/rustfs/rustfs-err.log

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload

sudo systemctl enable --now rustfs

```
### docker安装部署RustFS

```bash

git clone git@github.com:rustfs/rustfs.git

注释掉依赖
#depends_on:
#  - otel-collector

docker compose -f docker-compose.yml up -d rustfs

# 解决权限问题
# Initializing data directories: /data/rustfs0 /data/rustfs1 /data/rustfs2 /data/rustfs3
# mkdir -p /data/rustfs0
# mkdir: cannot create directory ‘/data/rustfs0’: Permission denied

sudo chown -R 10001:10001 ~/Software/rustfs

docker restart rustfs-server

```
