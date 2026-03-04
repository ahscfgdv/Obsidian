# 配置文件

## 文件路劲

`/etc/nginx`

```bash
remoteuser@vs3-ubuntu:/etc/nginx$ tree
.
├── conf.d
├── fastcgi.conf
├── fastcgi_params
├── koi-utf
├── koi-win
├── mime.types
├── modules-available
├── modules-enabled
├── nginx.conf
├── proxy_params
├── scgi_params
├── sites-available
│   └── default
├── sites-enabled
│   └── default -> /etc/nginx/sites-available/default
├── snippets
│   ├── fastcgi-php.conf
│   └── snakeoil.conf
├── uwsgi_params
└── win-utf
```

---
### 1. 核心配置文件

- **`nginx.conf`**: **主配置文件**。它是 Nginx 启动时加载的第一个文件。它定义了全局设置，如运行用户、工作进程数（worker processes）、日志路径，并使用 `include` 指令将其他目录（如 `conf.d` 和 `sites-enabled`）下的配置合并进来。

---

### 2. 站点配置目录（核心业务区）

这是你最常操作的地方：

- **`sites-available/`**: 存放**所有**站点的配置文件。你可以在这里为每个域名或应用创建一个独立的文件（例如 `my_app.conf`），但放在这里的配置**不会立即生效**。
    
- **`sites-enabled/`**: 存放**已激活**站点的符号链接（快捷方式）。Nginx 只会加载这个目录下的配置。
    
    > **技巧**：当你不想运行某个站点时，只需删除 `sites-enabled` 里的软链接，而保留 `sites-available` 里的源文件，方便以后恢复。
    
- **`conf.d/`**: 通用的第三方配置文件存放地。在这个结构中，它通常用于存放全局性的插件配置或不属于特定站点的逻辑。
    

---

### 3. 代理与网关协议参数

这些文件定义了 Nginx 与后端程序（如你的 Python Uvicorn 服务）通信时的“翻译规则”：

- **`proxy_params`**: 包含常用的反向代理请求头（如 `Host`, `X-Real-IP`, `X-Forwarded-For`）。在配置 Python 应用代理时，通常会 `include proxy_params;`。
    
- **`fastcgi.conf` / `fastcgi_params`**: 用于 PHP 等 FastCGI 服务的参数配置。
    
- **`uwsgi_params`**: 用于连接 Python uWSGI 服务器的参数。
    
- **`scgi_params`**: 用于 SCGI 协议的参数。
    

---

### 4. 辅助与编码工具

- **`mime.types`**: 定义了文件扩展名与 HTTP `Content-Type` 的对应关系（比如告诉浏览器 `.html` 是文本，`.png` 是图片）。
    
- **`snippets/`**: 存放可复用的配置片段（如 SSL 证书配置或通用的 PHP 处理逻辑），可以在多个站点配置中通过 `include` 引用。
    
- **`modules-available/` / `modules-enabled/`**: 类似于站点配置，用于管理 Nginx 的可选模块（如 Image Filter, GeoIP 等）。
    
- **`koi-utf` / `koi-win` / `win-utf`**: 用于字符集转换（主要针对俄语字符集），现代 Web 开发中很少直接改动。
    

---
