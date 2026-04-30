## 一：环境准备

确保Ubuntu 系统已经安装了图形桌面环境（Desktop Environment）。

**安装轻量级的 Xfce 桌面（远程连接更流畅）：**


```Bash
sudo apt update
sudo apt install xfce4 xfce4-goodies -y
```

---

## 二：安装 xrdp

1. **安装软件包**：
    
    ```Bash
    sudo apt install xrdp -y
    ```
    
2. **检查运行状态**：
    
    安装完成后，xrdp 服务会自动启动。
    
    ```Bash
    sudo systemctl status xrdp
    ```
    
    确认状态为 `active (running)`。
    
3. **将 xrdp 用户添加到 ssl-cert 组**：
    
    为了确保 xrdp 有权限读取系统的 SSL 证书（避免某些连接报错）：
    
    ```Bash
    sudo adduser xrdp ssl-cert
    ```
    

---

## 三：配置桌面环境 (针对 Xfce)

如果你安装了 Xfce，需要告诉 xrdp 在启动会话时使用它：

1. **创建/编辑配置文件**：
    
    ```Bash
    echo "xfce4-session" > ~/.xsession
    ```
    
2. **重启服务使配置生效**：
    
    ```Bash
    sudo systemctl restart xrdp
    
    ```
    
---

## 四：配置防火墙

默认情况下，xrdp 使用 **3389** 端口。如果你的系统开启了 `ufw` 防火墙，需要放行该端口：

*   **允许特定 IP 访问（更安全）**：
    ```bash
    sudo ufw allow from 192.168.1.0/24 to any port 3389
    ```
*   **允许所有 IP 访问（不推荐用于外网服务器）**：
    ```bash
    sudo ufw allow 3389
    ```

---

## 五：如何连接



1.  **在 Windows 电脑上**：
    *   按下 `Win + R`，输入 `mstsc` 并回车。
    *   在“计算机”一栏输入 Ubuntu 服务器的 **IP 地址**。
2.  **登录界面**：
    *   连接后，你会看到 xrdp 的登录窗口（通常名为 "Xvnc" 或 "Xorg"）。
    *   输入你的 **Ubuntu 用户名和密码**。
3.  **完成连接**：
    *   稍等片刻，你就能看到 Ubuntu 的图形桌面了。

---

## 六：创建新用户用于连接

---

 **创建新用户**

首先，在终端中使用 `adduser` 命令创建一个新用户（以 `remoteuser` 为例）：

```Bash
sudo adduser remoteuser
```

- 系统会提示你设置**密码**（输入时不会显示字符）。
    
- 随后的个人信息（姓名、电话等）可以直接按 `Enter` 跳过。
    
---

 **赋予必要的权限**

如果你希望这个新用户拥有安装软件或管理系统的权限（sudo 权限），请将其加入 sudo 组：

```Bash
sudo usermod -aG sudo remoteuser
```

为了确保该用户能正常使用远程桌面的安全证书：

```Bash
sudo usermod -aG ssl-cert remoteuser
```

---
 **为新用户配置桌面环境**

这是最关键的一步。每个用户都需要指定自己进入远程桌面时使用的界面（假设你已经按照之前的文档安装了 **Xfce**）：

1. **切换到新用户**：
    
    ```Bash
    su - remoteuser
    ```
    
2. **指定桌面会话**：
    
    ```Bash
    echo "xfce4-session" > ~/.xsession
    ```
    
---

**测试远程连接**

1. 在 Windows 上打开 **远程桌面连接 (mstsc)**。
    
2. 输入服务器 IP，点击连接。
    
3. 在 xrdp 登录窗口中，输入刚才创建的用户名 `remoteuser` 和你设置的密码。
    