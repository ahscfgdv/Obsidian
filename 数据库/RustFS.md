## 分布式对象存储


**官网**：`https://rustfs.com.cn/`

## 桶

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