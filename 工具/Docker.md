
# 安装


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
