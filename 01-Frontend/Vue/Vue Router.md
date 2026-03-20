在 Vue 项目中，**Vue Router**（通常简称为 Router）被称为前端的“导航管理员”。它的核心作用是：**建立 URL 路径与 Vue 组件之间的映射关系**。

如果没有 Router，你的 Vue 项目只是一个单页面（Single Page），无法通过改变地址栏来切换页面；有了 Router，你的应用才能像传统的网站一样，有“首页”、“关于页”、“用户中心”等不同的页面跳转。

---

## 1. 核心功能解析

### 路由映射 (Mapping)

Router 最基础的作用就是告诉 Vue：“当用户访问 `/login` 时，请在界面上展示 `Login.vue` 组件；当访问 `/dashboard` 时，展示 `Dashboard.vue`”。

### 声明式导航 (Declarative Navigation)

在 HTML 中我们用 `<a>` 标签跳转，但在 Vue Router 中，我们使用 `<router-link>`：

- **作用**：点击它会改变 URL，但**不会刷新浏览器页面**（这是单页面应用 SPA 的灵魂）。
    
- **代码**：`<router-link to="/home">首页</router-link>`
    

### 编程式导航 (Programmatic Navigation)

有时我们需要在代码逻辑里跳转（比如登录成功后自动跳到主页），这就需要用到 Router 提供的 API：

- **代码**：`router.push('/dashboard')`
    

### 嵌套路由 (Nested Routes)

在后台管理系统中，通常有一个固定的侧边栏和顶部栏，只有中间区域在变。Router 允许你定义“子路由”，让页面结构像剥洋葱一样层层嵌套。

---

## 2. 两个关键的内置组件

在你的 `App.vue` 或 `Layout.vue` 中，你一定会看到这两个组件：

|**组件**|**形象比喻**|**作用描述**|
|---|---|---|
|**`<router-link>`**|**传送门按钮**|改变地址栏 URL，并阻止浏览器默认刷新。|
|**`<router-view>`**|**舞台/占位符**|这是一个“坑”。当前 URL 匹配到哪个组件，那个组件就会显示在这个位置。|

---

## 3. 高级守卫：路由拦截（Navigation Guards）

Router 不仅仅是跳转，它还能当“保安”。最常用的功能是 **`beforeEach` (全局前置守卫)**：

- **作用**：在跳转发生前进行检查。
    
- **典型场景**：检查用户是否登录。如果没登录且想访问 `Admin` 页面，Router 就可以直接把他拦下，重定向到 `Login` 页面。
    

---

## 4. 路由传参

Router 负责在页面切换时传递数据。比如从“用户列表”跳到“用户详情”：

- **路径参数**：`/user/:id` -> `/user/123`
    
- **查询参数**：`/search?keyword=vue`
    

---

## 5. 项目中的实际流程

1. **用户操作**：点击了导航栏上的“订单中心”。
    
2. **触发 Router**：URL 变为 `localhost:5173/orders`。
    
3. **匹配配置**：Router 在 `router/index.ts` 配置表中找到路径 `/orders` 对应的是 `OrderList.vue`。
    
4. **渲染组件**：Router 将 `OrderList.vue` 的内容填入页面中的 `<router-view>` 区域。
    

---

### 总结

**Router 组件就是前端应用的“交警”和“地图”**。它确保了单页面应用（SPA）能够拥有多页面的体验，同时提供了权限控制、参数传递和页面嵌套的核心能力。

**你现在是准备在 `router/index.ts` 中配置你的第一个页面路由，还是想了解如何通过路由守卫来做登录权限校验？**