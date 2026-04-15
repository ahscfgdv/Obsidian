使用 Nginx 部署前端服务，本质上是将你开发好的静态文件（HTML、CSS、JS）交给 Nginx 这个高性能的 Web 服务器来管理。核心步骤只有三步：**打包项目、配置 Nginx、启动服务**。

下面是具体的操作流程和关键配置，帮你快速上手。

### 📦 第一步：打包你的前端项目
首先，需要将你的前端代码编译成浏览器可以直接识别的静态文件。
- **Vue项目**：在项目根目录运行 `npm run build`，会生成一个 `dist` 文件夹。
- **React项目**：运行 `npm run build`，通常会生成一个 `build` 文件夹。

将这个生成的文件夹（后文以 `dist` 为例）准备好，稍后要上传到服务器。

### ⚙️ 第二步：配置 Nginx
这是最关键的一步，需要修改 Nginx 的配置文件（通常是 `nginx.conf`），让它知道去哪里找你的文件以及如何处理请求。

#### 1. 基础静态服务配置
这是一个最基础的配置，作用是让 Nginx 监听 80 端口，并提供 `dist` 文件夹里的文件。
```nginx
server {
    listen       80;               # 监听端口
    server_name  your_domain.com;  # 你的域名或服务器IP

    location / {
        root   /path/to/your/dist; # 这里要替换成你的dist文件夹的实际路径
        index  index.html;          # 默认首页
    }
}
```

#### 2. 解决路由刷新404问题（关键！）
如果你的项目是单页应用（SPA）并使用了 `history` 模式（如Vue Router或React Router），直接使用上面的配置，刷新非首页的路径（如 `yourdomain.com/about`）时会出现 404 错误。这是因为浏览器向服务器发起了对该路径的请求，但服务器上并没有这个物理文件。

解决办法是使用 `try_files` 指令，让 Nginx 在找不到对应文件时，统一返回 `index.html`，由前端路由自己接管。
```nginx
server {
    listen       80;
    server_name  your_domain.com;
    root         /path/to/your/dist;

    location / {
        try_files $uri $uri/ /index.html; # 核心：尝试访问文件，失败则指向index.html
    }
}
```
- **`$uri`**: 尝试直接访问请求的路径（如 `/about`）。
- **`$uri/`**: 尝试将请求作为目录访问。
- **`/index.html`**: 如果前两者都失败了，就返回根目录下的 `index.html`。

如果你想在一个 Nginx 下部署多个前端项目，可以通过不同的路径来区分。例如，访问 `/admin` 时提供另一个项目的文件：
```nginx
location ^~ /admin {
    alias /path/to/another/dist; # 另一个项目的路径
    try_files $uri $uri/ /admin/index.html;
}
```

### 🚀 第三步：启动与验证
配置完成后，就可以启动或重启 Nginx 了。
1.  **测试配置**：在修改配置后，强烈建议先测试一下语法是否正确。
    ```bash
    nginx -t
    ```
    如果看到 `syntax is ok` 和 `test is successful`，说明配置没问题。

2.  **重载配置**：让新配置生效，并且这个过程是无中断的。
    ```bash
    nginx -s reload
    ```
    或者使用系统命令 `systemctl reload nginx`。

3.  **访问验证**：打开浏览器，输入你的域名或IP地址，应该就能看到你的前端页面了。

### 💡 进阶配置与优化
当你成功跑通基础流程后，可以考虑下面这些优化，让网站访问更快、更安全。

- **反向代理API**：前端通常需要请求后端接口。可以通过 Nginx 配置反向代理，轻松解决跨域问题，并将 `/api` 的请求统一转发到你的后端服务器。
  ```nginx
  location /api/ {
      proxy_pass http://your_backend_server_address; # 后端服务地址
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
  }
  ```

- **性能优化**：开启Gzip压缩可以大幅减小传输文件的大小，给静态资源设置缓存也能让页面加载更快。
  ```nginx
  gzip on; # 开启gzip压缩
  gzip_types text/plain text/css application/javascript text/xml application/xml;

  location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
      expires 1y; # 缓存一年
      add_header Cache-Control "public, immutable";
  }
  ```

- **配置HTTPS**：为了网站安全，强烈建议配置SSL证书开启HTTPS。使用Let's Encrypt可以免费获取证书。

如果在配置过程中遇到502或403等报错，可以告诉我具体的错误信息，我帮你一起看看～