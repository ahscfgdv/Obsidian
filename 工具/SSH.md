## 生成密钥

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
# ed25519
```

## 免密连接

上传公钥到服务器.ssh目录的authorized_keys中

# Commnd

```bash

ssh 192.168.100.27 -l hbyb #指定连接用户名

```

# scp传输文件

```bash

scp -P 22 example.txt user@remote_host:/home/user/

```