
# 安装部署

## Linux安装客户端

```bash
sudo apt install mariadb-client
```
## 普通安装Mysql



## Docker部署Mysql

部署 MySQL 通常有两种方式：**快速启动（命令行）** 和 **项目化部署（Docker Compose）**。

强烈建议使用 **Docker Compose** 方式，因为它更易于管理配置、数据卷和网络。

---

### 方法一：Docker Compose 部署（推荐 ✅）

这种方法把配置写在文件里，以后迁移或重启非常方便。

#### 1. 创建目录结构

先在你的服务器上创建一个文件夹来存放 MySQL 的配置和数据：

Bash

```
mkdir -p ~/mysql/data ~/mysql/conf ~/mysql/logs
cd ~/mysql
```

#### 2. 编写 `docker-compose.yml`

创建并编辑文件：

Bash

```
vim docker-compose.yml
```

粘贴以下内容（注意修改 `MYSQL_ROOT_PASSWORD`）：

YAML

```
version: '3.8'

services:
  mysql:
    image: mysql:8.0  # 建议指定具体版本，如 8.0 或 5.7
    container_name: mysql-server
    restart: always   # 容器挂了或重启服务器后自动启动
    ports:
      - "3306:3306"   # 宿主机端口:容器端口
    environment:
      MYSQL_ROOT_PASSWORD: "YourStrongPassword123" # ⚠️ 请修改这里
      MYSQL_DATABASE: "mydb"          # (可选) 启动时自动创建的数据库名
      TZ: "Asia/Shanghai"             # 设置时区
    volumes:
      - ./data:/var/lib/mysql         # 数据持久化挂载
      - ./conf:/etc/mysql/conf.d      # 配置文件挂载
      - ./logs:/var/log/mysql         # 日志文件挂载
    command: # 启动参数：设置字符集为 utf8mb4 (支持Emoji)
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_unicode_ci
      --default-authentication-plugin=mysql_native_password
```

#### 3. 启动服务

Bash

```
docker compose up -d
```

_(注：旧版 Docker 可能需要输入 `docker-compose up -d`)_

---

### 方法二：Docker CLI 命令行部署（快速测试）

如果你不想写文件，只想快速起一个库：

Bash

```
docker run -d \
  --name mysql-server \
  --restart always \
  -p 3306:3306 \
  -v ~/mysql/data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD="YourStrongPassword123" \
  -e TZ=Asia/Shanghai \
  mysql:8.0 \
  --character-set-server=utf8mb4 \
  --collation-server=utf8mb4_unicode_ci
```

---

### 💡 关键配置说明 (必读)

#### 1. 为什么要做数据挂载 (`-v`)？

**非常重要！** 容器是“用完即焚”的。

- 如果不挂载 `-v ./data:/var/lib/mysql`，当你删除容器 (`docker rm`) 时，**你的数据库数据会全部丢失**。
    
- 挂载后，数据实际存储在宿主机的 `~/mysql/data` 下，删除容器数据依然存在。
    

#### 2. 关于字符集 (`utf8mb4`)

默认的 MySQL 字符集可能是 `latin1`，这会导致中文乱码。

我在上面的配置中添加了 `command` 参数，强制开启 `utf8mb4`，这是目前最通用的字符集（支持中文和 Emoji 表情）。

#### 3. 身份验证插件 (`mysql_native_password`)

MySQL 8.0 默认使用了 `caching_sha2_password`。如果你使用较老的客户端（如旧版 Navicat 或老 Java 项目）连接报错，需要加上 `--default-authentication-plugin=mysql_native_password` 参数来兼容。

---

### 常用操作

#### 1. 进入 MySQL 容器内部

如果你想在命令行里执行 SQL：

Bash

```
docker exec -it mysql-server mysql -u root -p
# 然后输入密码
```

#### 2. 查看日志 (排错用)

如果启动失败，第一时间看日志：

Bash

```
docker logs -f mysql-server
```

#### 3. 外部连接

使用 Navicat / DBeaver / Workbench 连接时：

- **主机**: 服务器 IP 地址 (如果是本地则是 `127.0.0.1` 或 `localhost`)
    
- **端口**: `3306`
    
- **用户名**: `root`
    
- **密码**: 你设置的密码
    

#### 4. 备份数据

虽然做了挂载，但物理备份也很重要：

Bash

```
# 导出所有数据库到宿主机当前目录的 dump.sql
docker exec mysql-server mysqldump -u root -pYourPassword --all-databases > dump.sql
```

你需要我演示如何配置一个自定义的 `my.cnf` 文件来优化 MySQL 性能吗？

# 客户端

```bash

mysql -P 3306 -u root -p

```
