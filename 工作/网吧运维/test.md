
---


  1. 核心技术栈
   - 框架: FastAPI
   - 数据库: PostgreSQL/MySQL (通过 SQLAlchemy ORM)
   - 安全: JWT (JSON Web Tokens), Bcrypt (密码哈希)
   - 依赖管理: FastAPI Depends


  2. 用户模型 (User Model)
  用户信息存储在 sys_admin 表中，定义位于 server/app/models/user.py：
   - 表名: sys_admin
   - 字段:
       - id: 主键 ID
       - username: 唯一用户名
       - hashed_password: 存储经过 Bcrypt 加密的哈希值（严禁明文存储）
       - is_active: 账户激活状态

  3. 登录流程实现
  登录接口位于 server/app/api/v1/auth.py，系统提供了两种登录方式以兼容不同的前端需求：


  ##### A. 标准 OAuth2 表单登录 (/login)
   - 用途: 兼容 FastAPI 自动生成的 Swagger UI 调试。
   - 输入: OAuth2PasswordRequestForm (包含 username 和 password)。
   - 返回: 标准 OAuth2 格式的 Token 响应。


  ##### B. JSON 格式登录 (/login/json)
   - 用途: 现代前端框架（如 Vue/React）常用的 application/json 提交。
   - 逻辑:
       1. 查询用户: 根据 username 从数据库查询用户记录。
       2. 密码校验: 使用 bcrypt 校验明文密码与数据库中的哈希值是否匹配。
       3. 生成 Token: 校验通过后，将用户 ID (sub) 写入 JWT Payload，并设置过期时间。
       4. 响应: 返回统一格式的成功响应，包含 access_token。


  4. 安全机制 (Security)
  安全相关的底层实现位于 server/app/core/security.py：


   - 密码加密: 使用 bcrypt.hashpw 生成盐值并哈希。
   - 密码验证: 使用 bcrypt.checkpw 防止定时攻击。
   - JWT 生成:
       - 使用 python-jose 库。
       - Payload 包含 sub (用户ID) 和 exp (过期时间)。
       - 使用服务器私钥 SECRET_KEY 和 HS256 算法签名。


  5. 鉴权与当前用户获取 (Dependency)
  为了在其他接口中验证用户身份，系统在 server/app/api/deps.py 中定义了 get_current_user 依赖项：


   6. 提取 Token: 从 HTTP Header 的 Authorization: Bearer <token> 中提取。
   7. 解码验证: 校验 Token 是否过期、签名是否正确。
   8. 注入用户: 校验成功后，自动从数据库查询对应的 User 对象，并注入到业务函数中。

  使用示例:


   1 @router.get("/me")
   2 def get_me(current_user: User = Depends(get_current_user)):
   3     return success(data=current_user)


  9. 配置项
  相关安全配置位于 server/app/core/config.py 或 .env 文件中：
   - SECRET_KEY: 用于加密 JWT 的密钥（生产环境必须修改）。
   - ALGORITHM: 签名算法（默认 HS256）。
   - ACCESS_TOKEN_EXPIRE_MINUTES: Token 有效期（单位：分钟）。