在主流的移动端或 Web 应用中，为了平衡**安全性**与**用户体验**，通常不会简单地使用单一的短效 JWT。如果你发现 Token 一过期用户就必须重新登录，说明你目前的机制缺少**“无感刷新”**逻辑。

目前市面上实现“长时间登录”的主流方案是 **双 Token 机制（Access Token + Refresh Token）**。

---

## 1. 双 Token 机制的核心逻辑

这种方案将鉴权拆分为两个职责不同的令牌：

- **Access Token（访问令牌）**
    
    - **寿命短**：通常为 30 分钟到 2 小时。
        
    - **作用**：放在 HTTP Header 中，用于请求业务接口。
        
- **Refresh Token（刷新令牌）**
    
    - **寿命长**：通常为 7 天到 30 天。
        
    - **作用**：专门用于在 Access Token 过期时，向服务器换取一个新的 Access Token。它平时不参与业务请求，安全性更高。
        

---

## 2. 详细交互流程

### A. 登录阶段

1. 用户提交用户名密码。
    
2. 后端验证通过，返回 `access_token` 和 `refresh_token`。
    
3. 前端将两者保存在本地（如 `localStorage` 或 `Secure Cookie`）。
    

### B. 正常请求阶段

1. 前端在请求头携带 `access_token`。
    
2. 后端校验有效，正常返回数据。
    

### C. 自动刷新阶段（关键）

1. `access_token` 过期，后端返回 **401 Unauthorized**。
    
2. **前端拦截器**捕获到 401，**不要**跳转登录页，而是自动调用一个专门的 `/refresh` 接口，并携带 `refresh_token`。
    
3. 后端校验 `refresh_token` 有效，生成新的 `access_token` 返回。
    
4. 前端拿到新 Token，替换旧的，并**重新发起**刚才失败的那个业务请求。
    
5. 用户全程无感知，看到的是数据加载稍微慢了一点点，但没有被踢出系统。
    

---

## 3. 进阶：如何实现“无限续期”？

为了让用户像微信、抖音那样“只要一直在用就永远不掉线”，可以采用 **滑动过期（Sliding Expiration）**：

- 当用户使用 `refresh_token` 换取新的 `access_token` 时，后端判断 `refresh_token` 是否也快过期了（比如还剩不到 3 天）。
    
- 如果是，后端在返回新 `access_token` 的同时，**顺便返回一个新的 `refresh_token`**。
    
- 前端更新本地存储。这样只要用户每隔几天打开一次 App，他的登录态就会自动往后顺延。
    

---

## 4. 前端 Axios 拦截器实现伪代码

这是你在 Vue/React 项目中需要实现的核心逻辑：

JavaScript

```
// 响应拦截器
service.interceptors.response.use(
  response => response,
  async error => {
    const originalRequest = error.config;
    
    // 如果是 401 错误，且不是刷新接口本身报错
    if (error.response.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true; // 标记，防止死循环
      
      const refreshToken = localStorage.getItem('refresh_token');
      if (refreshToken) {
        try {
          // 1. 尝试换取新 Token
          const res = await axios.post('/api/refresh', { refresh_token: refreshToken });
          const { access_token } = res.data;
          
          // 2. 存储新 Token
          localStorage.setItem('access_token', access_token);
          
          // 3. 修改原请求的 Header 并重新发送
          originalRequest.headers['Authorization'] = `Bearer ${access_token}`;
          return service(originalRequest); 
        } catch (refreshError) {
          // Refresh Token 也失效了，彻底没救，跳登录页
          window.location.href = '/login';
        }
      }
    }
    return Promise.reject(error);
  }
);
```

---

## 5. 安全性补充：如何主动让用户掉线？

由于 JWT 是无状态的，如果你只是简单校验签名，一旦 Token 签发就无法收回。在主流应用中，通常会配合 **Redis** 使用：

1. 后端在 Redis 中存储 `refresh_token` 的白名单或版本号。
    
2. 如果用户点击“退出登录”或者修改了密码，后端在 Redis 中删除对应的 `refresh_token`。
    
3. 这样即便用户手里的 Token 没过期，下次刷新时也会因为 Redis 里找不到记录而失效。
    

### 对比单Token的优点

**降低 Token 泄露后的风险**

这是采用双 Token 最根本的原因。

- **单 Token 困境：** 为了不让用户频繁登录，单 Token 的有效期通常设置得很长（如 7 天或 30 天）。如果这个 Token 被黑客窃取，黑客在有效期内拥有持久的访问权限，且服务器很难在不更改密钥的情况下撤销该 Token。
    
- **双 Token 优势：**
    
    - **Access Token (AT)**：有效期极短（如 15 分钟）。即使被窃取，黑客能利用的时间窗口也非常有限。
        
    - **Refresh Token (RT)**：有效期长（如 7 天），但它通常不发送给业务 API，仅用于请求新的 AT。这意味着它暴露在网络传输中的频率更低，被截获的概率更小。

---

