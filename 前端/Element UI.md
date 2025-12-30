
**Element-Plus**：转为vue3设置的框架

## 安装使用

```bash

npm install element-plus

```

在Vue3的App.vue中导入使用

```js

import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import App from './App.vue'

const app = createApp(App)

app.use(ElementPlus)
app.mount('#app')

```
