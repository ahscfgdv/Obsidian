### 解决方案：更换为阿里云 yum 源

请按顺序执行以下命令：

**1. 备份原有的 yum 源配置文件**
为了防止配置错误，先备份系统默认的 repo 文件：
```bash
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

**2. 下载阿里云 CentOS 7 的 repo 文件**
使用 `curl` 命令直接下载阿里云提供的源配置文件：
```bash
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```
如果 `curl` 也报域名解析错误，可以在宿主机或能上网的电脑上访问 `http://mirrors.aliyun.com/repo/Centos-7.repo` 下载该文件，然后通过 SSH 或 FTP 工具上传到服务器的 `/etc/yum.repos.d/` 目录，并确保文件名为 `CentOS-Base.repo`。

**3. 清理并重建 yum 缓存**
让 yum 识别新的软件源地址：
```bash
yum clean all
yum makecache
```

**4. 重新安装 wget**
现在可以正常安装 `wget` 了：
```bash
yum install -y wget
```

---

### 如果问题依旧（网络与 DNS 排查）
如果更换源后仍报类似的网络错误，则需要检查系统的 DNS 设置或网络连通性：

- **测试网络连通性**：运行 `ping www.baidu.com` 和 `ping mirrors.aliyun.com`，确认域名能否正常解析。
- **配置公共 DNS**：如果域名无法解析，可以编辑 `/etc/resolv.conf` 文件，添加公共 DNS 服务器：
  ```bash
  echo -e "nameserver 8.8.8.8\nnameserver 114.114.114.114" | tee /etc/resolv.conf
  ```
  添加后再次尝试 `yum makecache`。