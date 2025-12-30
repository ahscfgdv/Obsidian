
## 创建项目

```bash

npm create vue@latest
npm install
npm run dev

```

## 目录结构

```bash

my-vue-app/
├── node_modules/       # 项目依赖库
├── public/              # 静态资源（不经过 Webpack/Vite 编译）
│   └── favicon.ico      # 网站图标
├── src/                 # 核心源代码
│   ├── assets/          # 静态资源（图片、字体、全局 CSS，会被编译）
│   ├── components/      # 公共组件（小型的、可复用的组件）
│   ├── router/          # 路由配置
│   ├── store/           # 状态管理 (Pinia 或 Vuex)
│   ├── views/           # 页面组件（通常是路由对应的完整页面）
│   ├── utils/           # 工具函数库 (axios 封装、日期格式化等)
│   ├── api/             # 后端接口定义
│   ├── App.vue          # 根组件（所有页面的入口）
│   └── main.js          # 入口文件（挂载插件、初始化 Vue 实例）
├── .gitignore           # Git 忽略文件配置
├── index.html           # 整个应用的 HTML 模板
├── package.json         # 项目信息、依赖列表及脚本命令
├── vite.config.js       # Vite 配置文件
└── README.md            # 项目说明文档

```
