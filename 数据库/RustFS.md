bug???? 本机不能用ip访问
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


## 因为Http代理导致通过本机IP访问无法访问

## 分布式对象存储


**官网**：`https://rustfs.com.cn/`

先不开启版本控制和对象锁可能存在bug

## 桶

存储桶命名不能用大写字母

## MC操作RustFS

**安装**

```bash

# 下载并安装 mc
wget https://dl.min.io/client/mc/release/linux-amd64/mc
chmod +x mc
sudo mv mc /usr/local/bin/

# 验证安装
mc --version

```

**连接**

```bash

mc alias set rustfs http://127.0.0.1:9000 rustfsadmin rustfsadmin

```

**文件操作**

```bash

mc ls rustfs

mc mb rustfs/bucket-creation-by-mc #创建桶

mc rb rustfs/bucket-creation-by-mc #删除桶

mc cp file_name rustfs/bucket-creation-by-mc #上传文件

mc rm rustfs/bucket-creation-by-mc/file_name #删除文件

mc get rustfs/bucket-creation-by-mc/file_name ./file_name #下载文件

```