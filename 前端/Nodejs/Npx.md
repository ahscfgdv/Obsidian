## 什么是Npx

`npx` 是 **Node 包执行器**（Node Package Execute），随 npm 5.2+ 版本自动安装。它的核心作用是：**直接运行 npm 包里的命令，而无需提前全局安装该包**。

### 与 `npm` 的关键区别
| 场景       | `npm`                            | `npx`                               |
| -------- | -------------------------------- | ----------------------------------- |
| 运行本地安装的包 | `./node_modules/.bin/vite` 需写全路径 | `npx vite` 自动查找 `node_modules/.bin` |
| 运行未安装的包  | 需先 `npm install -g <包名>`         | `npx <包名>` 临时下载执行，用完即删              |
| 执行一次性脚本  | 较繁琐                              | `npx create-react-app my-app` 一行搞定  |

### 为什么上文中会等价为 `npx vite --host --port 4567`？
因为 `vite` 命令来自项目本地 `node_modules/.bin/vite`，但直接在终端输入 `vite` 通常识别不到（除非全局安装了）。而 **`npx vite` 会自动查找本地的可执行文件并运行它**，效果等同于 `./node_modules/.bin/vite`。

### `npx` 的典型用法
- **试用工具包**：`npx cowsay "Hello"` 临时下载 cowsay 执行一次。
- **项目脚手架**：`npx create-vue my-project` 初始化项目（确保总是用最新版本）。
- **运行一次性脚本**：`npx http-server` 快速开启静态服务器。
- **避免全局污染**：用 `npx prettier --write .` 代替全局安装 Prettier。

简单记：`npx` 就是帮你**省去 `./node_modules/.bin/` 前缀，或免去全局安装的麻烦**。