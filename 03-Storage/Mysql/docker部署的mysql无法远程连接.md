### 建议修改后的配置

请将 `command` 部分修改为如下内容，重点是添加了 **`--skip-name-resolve`**：

YAML

```
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: mysql-server
    restart: always
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: "1234"
      MYSQL_DATABASE: "mydb"
      TZ: "Asia/Shanghai"
    volumes:
      - ./data:/var/lib/mysql
      - ./conf:/etc/mysql/conf.d
      - ./logs:/var/log/mysql
    command: 
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
      - --default-authentication-plugin=mysql_native_password
      - --skip-name-resolve  # <--- 关键！禁用DNS解析，防止握手超时
      - --connect_timeout=60 # <--- 增加握手超时容错
```

---

### 为什么加上这几行就能解决？

1. **`--skip-name-resolve`**: 这是解决 Docker MySQL `handshake` 错误的“特效药”。默认情况下，MySQL 会尝试检查每个连接进来的 IP 是否有对应的域名。在 Docker 复杂的虚拟网络中，这个查询经常会卡住直到超时，导致你看到“连接丢失”。
    
2. **`--connect_timeout=60`**: 给握手过程留出更多时间。如果你的服务器网络质量一般，默认的 10 秒可能不够完成整个安全验证。
    