
### 安装

**创建docker-compose.yaml文件**

```yaml

networks:
  gitea: # 创建docker网络

services:
  server:
    image: docker.gitea.com/gitea:1.24.7
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__database__DB_TYPE=mysql
      - GITEA__database__HOST=db:3306
      - GITEA__database__NAME=gitea
      - GITEA__database__USER=gitea
      - GITEA__database__PASSWD=gitea
    restart: always
    networks:
      - gitea
    volumes:
      - ./data:/data #将容器内的/data文件夹映射到./gitea文件夹
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000"
      - "222:22" #将容器的22端口转发到宿主机的222端口，避免端口冲突
    depends_on:
      - db #依赖服务
  db:
    image: docker.io/library/mysql:8
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=gitea
      - MYSQL_USER=gitea
      - MYSQL_PASSWORD=gitea
      - MYSQL_DATABASE=gitea
    networks:
      - gitea
    volumes:
      - ./mysql:/var/lib/mysql
```

**启动容器**

```yaml

docker-compose up server -d

```

**登录网页初始化配置**

http://192.168.100.26:3000

设置账号密码

**修改./data/gitea/conf/app.ini**

app.ini

```yaml

APP_NAME = Gitea: Git with a cup of tea
RUN_MODE = prod
RUN_USER = git
WORK_PATH = /data/gitea

[repository]
ROOT = /data/git/repositories
[repository.local]
LOCAL_COPY_PATH = /data/gitea/tmp/local-repo

[repository.upload]
TEMP_PATH = /data/gitea/uploads

[server]
APP_DATA_PATH = /data/gitea
DOMAIN = 10.3.0.172
SSH_DOMAIN = 10.3.0.172
HTTP_PORT = 3000
ROOT_URL = http://192.168.100.26:3000/
DISABLE_SSH = false
SSH_PORT = 222 #外部 SSH 访问端口（用户使用的端口）
SSH_LISTEN_PORT = 22 #Gitea 内部监听的 SSH 端口
LFS_START_SERVER = true
LFS_JWT_SECRET = j3BjPDF9t1ETZOfKdWPp-9mHg3z3xqSjKzHtpUWe0lk
OFFLINE_MODE = true

[database]

PATH = /data/gitea/gitea.db
DB_TYPE = mysql
HOST = db:3306
NAME = gitea
USER = gitea
PASSWD = gitea
LOG_SQL = false
SCHEMA =
SSL_MODE = disable

[indexer]
ISSUE_INDEXER_PATH = /data/gitea/indexers/issues.bleve

[session]

PROVIDER_CONFIG = /data/gitea/sessions
PROVIDER = file

[picture]

AVATAR_UPLOAD_PATH = /data/gitea/avatars
REPOSITORY_AVATAR_UPLOAD_PATH = /data/gitea/repo-avatars

[attachment]

PATH = /data/gitea/attachments

[log]

MODE = console
LEVEL = info
ROOT_PATH = /data/gitea/log

[security]

INSTALL_LOCK = true
SECRET_KEY =
REVERSE_PROXY_LIMIT = 1
REVERSE_PROXY_TRUSTED_PROXIES = *
INTERNAL_TOKEN = eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYmYiOjE3NjE5NzMyNjB9.7Sge3s1DOSFxCi9Mn26MApK25fnLY9NMlzwrfN01sKU
PASSWORD_HASH_ALGO = pbkdf2

[service]

DISABLE_REGISTRATION = false
REQUIRE_SIGNIN_VIEW = false
REGISTER_EMAIL_CONFIRM = false
ENABLE_NOTIFY_MAIL = false
ALLOW_ONLY_EXTERNAL_REGISTRATION = false
ENABLE_CAPTCHA = false
DEFAULT_KEEP_EMAIL_PRIVATE = false
DEFAULT_ALLOW_CREATE_ORGANIZATION = true
DEFAULT_ENABLE_TIMETRACKING = true
NO_REPLY_ADDRESS = noreply.localhost

[lfs]

PATH = /data/git/lfs

[mailer]

ENABLED = false
  
[openid]

ENABLE_OPENID_SIGNIN = true
ENABLE_OPENID_SIGNUP = true

[cron.update_checker]

ENABLED = false

[repository.pull-request]

DEFAULT_MERGE_STYLE = merge

[repository.signing]

DEFAULT_TRUST_MODEL = committer

[oauth2]

JWT_SECRET = MN3f5LOjyn-s7eMglIih8LF-D_fLtRSbo80iHnBNjtM

```

  

### 账号密码

admin
HBYB@123