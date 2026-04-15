## 什么是Npm

`npm` 是 **Node 包管理器**（Node Package Manager）的缩写，也是 Node.js 生态系统中**最核心的包管理工具**。它主要有三个身份：

- **包管理工具**：用于安装、卸载、更新、发布 JavaScript 包（库、框架、工具等）。
- **依赖管理器**：通过 `package.json` 和 `package-lock.json` 精确管理项目的依赖版本。
- **脚本执行器**：通过 `npm run <script>` 执行 `package.json` 中 `scripts` 字段定义的命令。

### npm 的核心功能

| 功能 | 常用命令示例 | 说明 |
|------|-------------|------|
| 初始化项目 | `npm init` / `npm init -y` | 生成 `package.json` 文件 |
| 安装依赖 | `npm install <包名>` | 安装到 `node_modules`，并写入 `dependencies` |
| 安装开发依赖 | `npm install -D <包名>` | 安装到 `devDependencies`（如测试工具、打包器） |
| 全局安装 | `npm install -g <包名>` | 安装到系统全局，可在任意目录执行命令 |
| 卸载依赖 | `npm uninstall <包名>` | 从 `node_modules` 和 `package.json` 中移除 |
| 运行脚本 | `npm run dev` / `npm run build` | 执行 `package.json` 中定义的脚本命令 |
| 更新依赖 | `npm update` | 根据 `package.json` 的版本范围更新包 |
| 发布包 | `npm publish` | 将你自己的包发布到 npm 官方仓库 |

### npm 与 Node.js 的关系
- npm 随 Node.js **自动安装**，安装 Node.js 后即可在终端使用 `npm` 命令。
- 几乎所有前端或 Node.js 项目的**起点都是 `npm init`**，依赖安装和管理都离不开它。

### 关键文件
- **`package.json`**：项目的清单文件，记录项目名称、版本、依赖列表、脚本命令等。
- **`package-lock.json`**：锁定实际安装的依赖版本，确保团队成员或部署环境安装的依赖版本完全一致。

### 与其他包管理器的对比
| 工具 | 特点 |
|------|------|
| `npm` | 官方默认工具，生态最广，v7 后性能大幅优化 |
| `yarn` | Facebook 出品，早期因速度和确定性锁文件流行，现在与 npm 功能趋同 |
| `pnpm` | 磁盘空间利用率高，依赖硬链接，安装速度快 |

### 总结
`npm` 是 JavaScript 世界的**基础设施**——没有它，前端开发中安装 Vue、React、Vite 等工具将变得极其繁琐。你之前执行的 `npm run dev` 就是通过 npm 的脚本运行功能来启动 Vite 开发服务器的。


## 安装

### Linux


### 配置

```bash

npm config set registry https://registry.npmmirror.com 
#配置镜像源

```

### 其他

```bash

nodejs -v

```

## 命令

```bash
npm list -g --depth=0 
# 查看安装的所有全局工具
npm update -g @google/gemini-cli
# 更新全局工具
```