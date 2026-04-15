JSDoc 是 JavaScript 的一套**标记语言和注释规范**，通过在代码中添加特定格式的注释，可以为函数、变量、类等提供**类型信息、参数说明、返回值描述和元数据**。它主要有三个作用：

- **生成文档**：配合工具（如 JSDoc 命令行、TypeDoc）自动生成可浏览的 API 文档网站。
- **智能提示**：VS Code、WebStorm 等编辑器会解析 JSDoc 注释，在调用函数时显示参数类型和说明。
- **类型检查**：即使不写 TypeScript，用 JSDoc 也能让 TypeScript 编译器或 VS Code 对 `.js` 文件进行类型推断和报错提示。

### 基本语法示例

```javascript
/**
 * 计算两个数的和。
 * @param {number} a - 第一个加数
 * @param {number} b - 第二个加数
 * @returns {number} 两数之和
 */
function add(a, b) {
    return a + b;
}
```

### 常用标签速览

| 标签 | 用途 | 示例 |
|------|------|------|
| `@param {type} name - desc` | 描述参数类型和含义 | `@param {string} name - 用户名` |
| `@returns {type} desc` | 描述返回值类型 | `@returns {Promise<User>} 用户对象` |
| `@typedef {Object} TypeName` | 定义自定义类型 | `@typedef {{x: number, y: number}} Point` |
| `@property {type} name - desc` | 描述对象属性（用在 `@typedef` 内） | `@property {string} id - 唯一标识` |
| `@type {type}` | 声明变量或属性的类型 | `/** @type {number[]} */ const scores = [];` |
| `@template T` | 定义泛型 | `@template T` + `@param {T} value` |
| `@callback` | 描述回调函数签名 | `@callback {(data: string) => void} Callback` |
| `@deprecated` | 标记已弃用 | `@deprecated 请使用 newMethod 代替` |

### 与 TypeScript 的关系

- TypeScript 从 2.3 开始支持在 `.js` 文件中通过 **JSDoc 注释来获得类型能力**，无需完全迁移到 `.ts`。
- 许多纯 JavaScript 项目（尤其是库和工具链）用 JSDoc 既能保持代码为纯 JS，又能享受类型安全和智能提示。

### 工具集成

- **生成文档**：`npm install -g jsdoc` 后运行 `jsdoc yourfile.js`
- **TypeScript 类型检查**：在 `jsconfig.json` 中设置 `"checkJs": true` 即可。

更多标签参考可查阅官方文档：[JSDoc 官方手册](https://jsdoc.app/)