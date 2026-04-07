# QASystem 数据库模型说明文档

本文档总结了 QASystem 后端数据库的主要表结构及其字段作用。系统使用 Alembic 进行数据库迁移管理。

---

## 1. 用户与权限模块

### 1.1 系统管理员表 (`sys_admin`)
记录拥有后台管理权限的人员。

| 字段名               | 类型          | 描述               |
| :---------------- | :---------- | :--------------- |
| `id`              | Integer     | 管理员唯一 ID (主键)    |
| `username`        | String(50)  | 登录用户名 (唯一索引)     |
| `nickname`        | String(50)  | 管理员昵称            |
| `hashed_password` | String(255) | 加密后的密码哈希         |
| `is_active`       | Boolean     | 账号是否启用 (默认 True) |

### 1.2 普通用户表 (`app_user`)
系统的前端使用者，即提问和提交工单的主体。

| 字段名                | 类型          | 描述               |
| :----------------- | :---------- | :--------------- |
| `id`               | Integer     | 用户唯一 ID (主键)     |
| `username`         | String(50)  | 登录用户名 (唯一索引)     |
| `hashed_password`  | String(255) | 加密后的密码哈希         |
| `is_active`        | Boolean     | 账号是否启用 (默认 True) |
| `customer_name`    | String(100) | 客户真实姓名/昵称        |
| `customer_ip`      | String(50)  | 客户公网IP           |
| `customer_address` | String(225) | 客户地址             |
| `create_time`      | DateTime    | 账户创建时间           |
| `update_time`      | DateTime    | 最后一次修改时间         |

---

## 2. 知识库模块

### 2.1 文档分类表 (`document_category`)
对知识库中的 PDF/Word 文档进行分类管理。

| 字段名           | 类型         | 描述           |
| :------------ | :--------- | :----------- |
| `id`          | Integer    | 分类唯一 ID (主键) |
| `name`        | String(50) | 分类名称 (唯一索引)  |
| `create_time` | DateTime   | 分类创建时间       |

### 2.2 文档元数据表 (`document`)
记录已上传或待审核的知识库文档信息。

| 字段名           | 类型          | 描述                                                       |
| :------------ | :---------- | :------------------------------------------------------- |
| `id`          | Integer     | 文档唯一 ID (主键)                                             |
| `filename`    | String(255) | 文件名称                                                     |
| `source_path` | String(512) | 源文件在服务器上的存储路径                                            |
| `category_id` | Integer     | 关联的分类 ID                                                 |
| `md5`         | String(32)  | 文件的 MD5 哈希值，用于快速查重                                       |
| `status`      | String(20)  | 审核状态: `pending` (待审), `published` (已发布), `rejected` (驳回) |
| `reviewer_id` | Integer     | 审核人 ID (对应 `sys_admin.id`)                               |
| `create_time` | DateTime    | 上传时间                                                     |
| `update_time` | DateTime    | 更新时间                                                     |

---

## 3. 会话与历史模块

### 3.1 聊天消息表 (`chat_messages`)
持久化存储用户的对话历史，用于 RAG 上下文维持。

| 字段名          | 类型          | 描述                                     |
| :----------- | :---------- | :------------------------------------- |
| `id`         | Integer     | 消息唯一 ID (主键)                           |
| `user_id`    | Integer     | 关联的用户 ID                               |
| `session_id` | String(255) | 会话唯一标识符 (通常格式为 `{user_id}_{yyyymmdd}`) |
| `message`    | JSON        | 消息内容 (包含 `role` 和 `content` 等结构)       |
| `created_at` | DateTime    | 消息生成时间                                 |

---

## 4. 工单管理模块

### 4.1 技术支持工单表 (`app_ticket`)
记录用户手动提交的技术问题支持请求。

| 字段名              | 类型          | 描述                                                  |
| :--------------- | :---------- | :-------------------------------------------------- |
| `id`             | Integer     | 工单唯一 ID (主键)                                        |
| `user_id`        | Integer     | 提交人的用户 ID                                           |
| `customer_name`  | String(100) | 提交时自动关联的客户名称                                        |
| `machine_number` | String(50)  | 涉及的问题机器号                                            |
| `question`       | Text        | 问题简述/工单标题                                           |
| `description`    | Text        | 详细的问题背景描述                                           |
| `problem_image`  | String(255) | 描述问题的截图文件路径                                         |
| `solution_pdf`   | String(255) | 最终生成的解决方案 PDF 文档路径                                  |
| `status`         | String(20)  | 当前状态: `pending`, `processing`, `resolved`, `closed` |
| `resolution`     | Text        | 管理员填写的处理闭环总结                                        |
| `created_at`     | DateTime    | 工单创建时间                                              |
