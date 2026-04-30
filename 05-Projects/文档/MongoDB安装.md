​`https://www.mongodb.com/try/download/community​`

下载地址

选择对应的系统版本和服务

​​![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20260430150934593_2026-04-30_150934.png)


点击copy link​在服务器上用wget下载

```bash
wget https://repo.mongodb.org/apt/ubuntu/dists/noble/mongodb-org/8.2/multiverse/binary-amd64/mongodb-org-server_8.2.1_amd64.deb
sudo dpkg -i mongodb-org-server_8.2.1_amd64.deb
sudo systemctl enable mongod
sudo systemctl start mongod
```

修改配置文件/etc/mongod.conf​允许远程连接

```bash
# mongod.conf

# for documentation of all options, see:
#   http://docs.mongodb.org/manual/reference/configuration-options/

# Where and how to store data.
storage:
  dbPath: /var/lib/mongodb
#  engine:
#  wiredTiger:

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0 #修改127.0.0.1为0.0.0.0


# how the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo

#security:

#operationProfiling:

#replication:

#sharding:

## Enterprise-Only Options:

#auditLog:
```

下载mongosh

```bash
wget https://downloads.mongodb.com/compass/mongodb-mongosh_2.5.9_amd64.deb
sudo dpkg -i mongodb-mongosh_2.5.9_amd64.deb
```

### ubuntu安装MongoDB

```bash
# apt安装
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
sudo apt install -y mongodb-org
sudo systemctl start mongod
sudo systemctl enable mongod
```