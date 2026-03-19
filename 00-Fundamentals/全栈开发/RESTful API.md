## 1. 核心设计原则

### 资源（Resources）与 URI

在 REST 架构中，每个 URL 代表一种**资源**。

- **使用名词而非动词**：不要在 URL 中包含操作动作。
    
    - ❌ 错误：`/getUsers` 或 `/deleteOrder`
        
    - ✅ 正确：`/users` 或 `/orders`
        
- **使用复数形式**：为了保持一致性，通常建议使用复数。
    
    - ✅ 正确：`/articles/123`
        

### 标准 HTTP 方法（Verbs）

通过 HTTP 的动作来决定对资源执行什么操作，这被称为“语义化”：

|**方法**|**对应操作 (CRUD)**|**描述**|
|---|---|---|
|**GET**|Read (读取)|获取资源列表或单个资源详情|
|**POST**|Create (创建)|新建一个资源|
|**PUT**|Update (更新)|更新资源的全部信息（客户端需提供完整实体）|
|**PATCH**|Update (更新)|更新资源的部分信息|
|**DELETE**|Delete (删除)|删除某个资源|

---

## 2. 状态码（Status Codes）

服务器向用户返回的状态码应该精确反映操作结果。常见的包括：

- **2xx (成功)**
    
    - `200 OK`：请求成功。
        
    - `201 Created`：资源创建成功（常用于 POST）。
        
- **4xx (客户端错误)**
    
    - `400 Bad Request`：请求参数有误。
        
    - `401 Unauthorized`：未认证（没登录）。
        
    - `403 Forbidden`：已登录但没权限。
        
    - `404 Not Found`：资源不存在。
        
- **5xx (服务器错误)**
    
    - `500 Internal Server Error`：服务器内部出错了。
        

---

## 3. 数据交互规范

### 过滤与分页

对于获取列表的接口，不应一次性返回所有数据，而是通过查询参数（Query Parameters）进行过滤和分页：

> `GET /users?page=2&size=10&gender=male`

### 无状态性（Stateless）

服务器不应保存客户端的会话状态。每一次请求都必须包含处理该请求所需的全部信息（如 Token），这使得服务端更容易水平扩展。

---

## 4. 响应数据结构

通常使用 **JSON** 作为数据传输格式。建议保持结构统一，例如：

JSON

```
{
  "code": 200,
  "message": "success",
  "data": {
    "id": 1,
    "username": "gemini_user"
  }
}
```

---

