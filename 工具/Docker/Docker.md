
# 安装

## Ubuntu安装Docker

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

# Command


```bash

docker version

```

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

## 配置Http代理

在 Docker 中配置 HTTP 代理其实比想象中复杂一点，因为你可能需要在**三个不同的层面**进行配置：**Docker 守护进程（拉取镜像）**、**容器运行环境（运行时）**以及 **Build 阶段（构建镜像）**。

---

### 1. 为 Docker Daemon 配置代理 (用于 `docker pull`)

如果你发现 `docker pull` 镜像非常慢或者报错，你需要配置守护进程代理。这是最常用的配置。

1. **创建目录：**
    
    Bash
    
    ```
    sudo mkdir -p /etc/systemd/system/docker.service.d
    ```
    
2. **创建配置文件：** `sudo nano /etc/systemd/system/docker.service.d/http-proxy.conf`
    
3. **写入以下内容：**
    
    Ini, TOML
    
    ```
    [Service]
    Environment="HTTP_PROXY=http://proxy.example.com:8080/"
    Environment="HTTPS_PROXY=http://proxy.example.com:8080/"
    Environment="NO_PROXY=localhost,127.0.0.1,docker-registry.example.com"
    ```
    
4. **重启 Docker：**
    
    Bash
    
    ```
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```
    

---

### 2. 为容器配置代理 (用于容器内上网)

如果你希望进入容器后（如 `apt-get install`）能走代理，可以全局配置 `~/.docker/config.json`。

**在你的宿主机当前用户目录下修改：**

JSON

```
{
 "proxies": {
   "default": {
     "httpProxy": "http://proxy.example.com:8080",
     "httpsProxy": "http://proxy.example.com:8080",
     "noProxy": "localhost,127.0.0.1"
   }
 }
}
```

_配置完成后，之后新建的容器会自动注入这些环境变量。_

---

### 3. 在 Build 阶段配置代理 (用于 `docker build`)

如果在 `Dockerfile` 里执行 `npm install` 或 `pip install` 需要代理，建议在执行命令时动态传入，避免把代理地址写死在镜像里。

**执行构建命令时：**

Bash

```
docker build \
  --build-arg "HTTP_PROXY=http://127.0.0.1:20171/" \
  --build-arg "HTTPS_PROXY=http://127.0.0.1:20171/" \
  --build-arg "NO_PROXY=localhost,127.0.0.1" \
  -t my-app .
```

---

### 4. 常见问题排查（避坑指南）

- **地址问题：** 如果你的代理软件跑在宿主机上，地址写 `127.0.0.1` 往往无效（因为容器认为 127.0.0.1 是它自己）。请使用宿主机的局域网 IP，或者在 Linux 上使用 `172.17.0.1`（Docker 网桥默认地址）。
    
- **协议匹配：** 即使是 `HTTPS_PROXY`，其值通常也是 `http://...`，除非你的代理服务器本身强制要求加密连接。
    
- **NO_PROXY 的重要性：** 一定要加上 `localhost` 和 `127.0.0.1`，否则容器内部的进程通信可能会被错误地转发到代理服务器，导致服务无法访问。
    

---

**你是遇到了 `docker pull` 报错的问题，还是在容器内部运行程序需要访问外部 API？我可以根据你的具体场景提供更精准的地址建议。**
## 设置linux无需sudo

**将用户添加到docker用户组**

```bash

sudo usermod -aG docker $USER

newgrp docker

```
