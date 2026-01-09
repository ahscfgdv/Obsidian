
## 安装部署

### docker安装部署RustFS

```bash

git clone git@github.com:rustfs/rustfs.git

%% 注释掉 %%

```

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