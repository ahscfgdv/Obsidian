
# 安装

## Ubuntu安装

### 1. 卸载旧版本（可选）

如果你的系统之前安装过旧版的 Docker（比如 `docker.io` 或 `docker-engine`），先清理它们以防冲突：

```
sudo apt remove docker docker-engine docker.io containerd runc
```

### 2. 设置 Docker 仓库

在安装 Docker 引擎之前，需要设置 Docker 仓库，以便后续可以从仓库安装和更新。

**第一步：更新索引并安装必要依赖**


```
sudo apt update
sudo apt install ca-certificates curl gnupg
```

**第二步：添加 Docker 的官方 GPG 密钥**

```
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

**第三步：设置仓库地址**

```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 3. 安装 Docker 引擎

更新包索引并安装最新版本的 Docker：

Bash

```
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

---

### 4. 验证安装是否成功

运行经典的 `hello-world` 镜像来确认：

```
sudo docker run hello-world
```

如果看到 "Hello from Docker!" 的字样，说明安装成功。

---

# 配置

## 配置镜像源

### **临时换源**

`sudo docker pull docker.m.daocloud.io/hello-world`

### **修改配置文件**

`sudo vim /etc/docker/daemon.json`
 
```
{
    "registry-mirrors": [
        "https://registry.docker-cn.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://hub-mirror.c.163.com",
        "https://mirror.baidubce.com",
        "https://ccr.ccs.tencentyun.com"
    ]
}
```
 
### 重启docker

```bash

sudo systemctl daemon-reload #重启daemon进程 
sudo systemctl restart docker #重启docker


docker info #查看docker配置
```

## 设置linux无需sudo

**将用户添加到docker用户组**

```bash

sudo usermod -aG docker $USER

newgrp docker

```
