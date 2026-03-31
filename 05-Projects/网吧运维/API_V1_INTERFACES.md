# QASystem API v1 接口文档

本文档总结了后端服务 `v1` 版本的所有 API 接口的详细请求参数和返回参数说明。所有接口基础路径均为 `/api/v1`。

## 通用响应格式
所有接口均返回统一的 JSON 格式：
```json
{
    "code": 200,
    "msg": "success",
    "data": { ... }
}
```

---

## 1. 认证模块 (Authentication)

### 1.1 用户登录
- **路径**: `POST /auth/login`
- **描述**: 支持 OAuth2 标准表单格式的普通用户登录。
- **请求参数 (Form Data)**:
  - `username`: 用户名
  - `password`: 密码
- **返回数据 (`data`)**:
  - `access_token`: JWT 访问令牌
  - `token_type`: 令牌类型 (通常为 "bearer")

### 1.2 用户登录 (JSON)
- **路径**: `POST /auth/login/json`
- **描述**: 支持 JSON 格式请求体的普通用户登录。
- **请求参数 (JSON)**:
  - `username`: 用户名
  - `password`: 密码
- **返回数据 (`data`)**: 同上。

### 1.3 管理员登录
- **路径**: `POST /manager/auth/login`
- **描述**: 管理后台专用登录接口。
- **请求参数 (Form Data)**:
  - `username`: 用户名
  - `password`: 密码
- **返回数据 (`data`)**: 同上。

---

## 2. 知识库与 RAG 模块 (RAG & Knowledge Base)

### 2.1 对话接口
- **普通对话**: `POST /rag/chat`
  - **请求参数 (JSON)**:
    - `question`: 提问内容 (必填)
    - `session_id`: 会话 ID (可选，默认按天隔离)
  - **返回数据 (`data`)**:
    - `answer`: AI 回答内容
    - `docs`: 参考文档列表
      - `page_content`: 文档片段内容
      - `metadata`: 文档元数据
      - `file_url`: 文档预览 URL
    - `session_id`: 当前会话 ID

- **流式对话**: `POST /rag/chat/stream`
  - **请求参数**: 同上。
  - **返回**: SSE 流 (`text/event-stream`)，每行包含 `answer` 片段或 `docs`。

### 2.2 文档管理
- **上传文档**: `POST /rag/upload`
  - **请求参数 (Multipart)**:
    - `file`: PDF/DOC/DOCX 文件
  - **返回数据**: 成功消息。

- **获取知识库文件**: `GET /rag/file/{filename}`
  - **描述**: 预览 PDF 文件。

- **文档列表**: `GET /rag/documents`
  - **查询参数**:
    - `status`: 过滤状态 (可选: `pending`, `published`, `rejected`)
  - **返回数据 (`data`)**: 文档对象列表，包含 `id`, `filename`, `category_id`, `status` 等。

---

## 3. 工单管理 (Ticket Management)

### 3.1 创建工单
- **路径**: `POST /ticket/create`
- **请求参数 (JSON)**:
  - `question`: 问题简述 (必填)
  - `description`: 详细描述 (可选)
  - `machine_number`: 机器号 (可选)
  - `problem_image`: 问题图片路径 (可选)
- **返回**: 成功消息。

### 3.2 更新工单 (管理员)
- **路径**: `PUT /ticket/{ticket_id}`
- **请求参数 (JSON)**:
  - `status`: 状态 (`pending`, `processing`, `resolved`, `closed`)
  - `resolution`: 处理结果/解决方案 (可选)
  - `solution_pdf`: 解决方案文件路径 (可选，若是 .docx 会自动转 .pdf 并入库)
- **返回数据 (`data`)**: 更新后的工单详情对象。

---

## 4. 文档分类管理 (Category Management)

### 4.1 新增分类
- **路径**: `POST /category/`
- **请求参数 (JSON)**:
  - `name`: 分类名称 (必填)
- **返回数据 (`data`)**: `id`, `name`, `create_time`

---

## 5. 用户管理 (User Management - 管理员权限)

### 5.1 创建用户
- **路径**: `POST /user/`
- **请求参数 (JSON)**:
  - `username`: 用户名 (必填)
  - `password`: 密码 (必填)
  - `customer_name`: 客户姓名 (可选)
  - `is_active`: 是否激活 (默认 true)
- **返回数据 (`data`)**: 用户详情对象 (包含 `id`, `create_time` 等)。

### 5.2 更新用户
- **路径**: `PUT /user/{user_id}`
- **请求参数 (JSON)**: `username`, `password`, `customer_name`, `is_active` (均为可选)

---

## 6. 工具与集成 (Tools & Integration)

### 6.1 OCR 识别
- **路径**: `POST /ocr/image`
- **请求参数 (Multipart)**:
  - `file`: 图片文件
- **返回数据 (`data`)**:
  - `text`: 识别出的文本内容

### 6.2 通用文件上传
- **路径**: `POST /upload/file`
- **请求参数 (Multipart)**:
  - `file`: 待上传文件
- **返回数据 (`data`)**:
  - `url`: 文件访问路径
  - `filename`: 保存后的文件名

### 6.3 钉钉集成
- **路径**: `POST /dd/getAccessToken`
- **请求参数 (JSON)**:
  - `appKey`: 钉钉应用 AppKey
  - `appSecret`: 钉钉应用 AppSecret
- **返回数据 (`data`)**: 钉钉接口原始返回的 Token 信息。
