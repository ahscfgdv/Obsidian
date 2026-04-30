# PyMongo MongoDB CRUD 操作说明文档

本文档基于提供的 **MongoDB CRUD 完整示例代码**，详细讲解 Python 使用 `pymongo` 操作 MongoDB 数据库的全流程，包含**连接初始化、数据库 / 集合管理、文档增删改查、计数查询、数据清理**等核心功能，覆盖 MongoDB 最常用的生产级操作，可直接用于学习、测试与生产环境开发。

## 一、前置依赖与环境准备

### 1. 安装依赖库

```bash
uv add pymongo
```

### 2. 配置文件准备

创建 `config_data.py` 配置文件，存储 MongoDB 连接地址（**禁止硬编码在业务代码中**）：

```python
# config_data.py
# MongoDB连接URI（本地/远程服务器通用）
# 本地无密码：mongodb://localhost:27017/
# 远程带密码：mongodb://用户名:密码@ip:端口/
MONGO_URI = "mongodb://localhost:27017/"
```

### 3. 核心概念

- **客户端（MongoClient）**：MongoDB 连接入口，管理所有数据库连接
    
- **数据库（Database）**：MongoDB 的顶级容器，类似 MySQL 的数据库
    
- **集合（Collection）**：数据库中的分组容器，**对应 MySQL 的表**
    
- **文档（Document）**：集合中存储的数据单元，**对应 MySQL 的行**，格式为 JSON / 字典
    
- **_id**：MongoDB 自动生成的唯一主键，文档的唯一标识
    

---

## 二、代码核心模块详解

### 模块 1：初始化 MongoDB 客户端（建立连接）

```python
client = MongoClient(config.MONGO_URI)
```

#### 功能

创建 MongoDB 连接客户端，支持本地 / 远程服务器连接

#### 连接 URI 格式

- 无认证本地：`mongodb://localhost:27017/`
    
- 带认证远程：`mongodb://user:password@ip:27017/`
    

#### 异常场景

- MongoDB 服务未启动：抛出 `ConnectionFailure`
    
- 认证信息错误：抛出 `OperationFailure`
    

---

### 模块 2：选择数据库与集合

```python
# 选择数据库（不存在则自动创建）
db = client[db_name]
# 选择集合（不存在则自动创建）
collection = db[collection_name]
```

#### 核心特性

MongoDB **无需提前创建数据库 / 集合**，第一次插入数据时会自动创建，无需手动建库建表。

---

### 模块 3：CREATE - 插入单条文档

```python
post_data = {
    "title": "Hello, MongoDB!",
    "content": "This is a test document...",
    "author": "Gemini-CLI",
    "project": "QASystem",
    "tags": ["test", "mongodb", "demo"],
    "created_at": datetime.datetime.now()
}
result = collection.insert_one(post_data)
document_id = result.inserted_id
```

#### 功能

向集合中插入**单条文档数据**

#### 关键字段

- `insert_one()`：插入单条数据的标准 API
    
- `inserted_id`：返回 MongoDB 自动生成的唯一主键`_id`
    
- 支持数据类型：字符串、数字、列表、时间、嵌套字典等
    

#### 扩展：批量插入数据

```python
# 批量插入多条文档
data_list = [{"name": "test1"}, {"name": "test2"}]
collection.insert_many(data_list)
```

---

### 模块 4：READ - 查询单条文档

```python
query_result = collection.find_one({"_id": document_id})
```

#### 功能

根据条件**查询单条匹配的文档**

#### 参数说明

- `{"_id": document_id}`：查询条件（字典格式）
    
- `find_one()`：仅返回第一条匹配结果，性能最优
    

#### 常用查询条件

```python
# 按普通字段查询
collection.find_one({"author": "Gemini-CLI"})
# 模糊匹配
collection.find_one({"title": {"$regex": "MongoDB"}})
```

---

### 模块 5：COUNT - 统计文档数量

```python
count = collection.count_documents({})
```

#### 功能

统计集合中符合条件的文档总数

#### 参数说明

- `{}`：空条件表示统计**所有文档**
    
- 支持带条件统计：`count_documents({"author": "Gemini-CLI"})`
    

---

### 模块 6：LIST - 查询所有文档（分页）

```python
cursor = collection.find({}).limit(5)
for i, doc in enumerate(cursor):
    print(f"文档 {i+1}: {doc.get('title')}")
```

#### 功能

查询集合中所有文档，支持**分页、排序、过滤**

#### 核心 API

- `find({})`：查询所有数据
    
- `limit(5)`：限制返回 5 条数据（生产环境必备，避免全表扫描）
    
- 返回值：游标对象（Cursor），可循环遍历读取
    

---

### 模块 7：UPDATE - 更新单条文档

```python
new_values = {"$set": {
    "content": "This is updated content...", 
    "updated_at": datetime.datetime.now()
}}
collection.update_one({"_id": document_id}, new_values)
```

#### 功能

根据条件**更新单条文档**

#### 核心关键字

- `update_one()`：仅更新第一条匹配数据
    
- `$set`：**仅更新指定字段**，不覆盖整个文档（推荐使用）
    
- 可新增字段、修改字段、更新时间戳
    

#### 扩展：批量更新

```python
# 更新所有匹配条件的文档
collection.update_many({"author": "Gemini-CLI"}, new_values)
```

---

### 模块 8：DELETE - 删除单条文档

```python
collection.delete_one({"_id": document_id})
```

#### 功能

根据条件删除单条文档

#### 扩展：批量删除

```python
# 删除所有匹配条件的文档
collection.delete_many({"author": "Gemini-CLI"})
```

---

### 模块 9：清理数据（删除集合 + 数据库）

```python
# 删除集合（对应删表）
db.drop_collection(collection_name)
# 删除数据库（对应删库）
client.drop_database(db_name)
```

#### 关键规则

1. 删除集合：清空集合内所有文档，不可恢复
    
2. 删除数据库：清空数据库内所有集合，**谨慎操作**
    
3. 测试代码执行完成后自动清理，无残留数据
    

---

## 三、完整代码运行说明

### 1. 执行流程

1. 连接 MongoDB 服务
    
2. 自动创建测试数据库 + 集合
    
3. 插入单条测试数据 → 获取自动生成 ID
    
4. 根据 ID 查询数据 → 统计数据量 → 列出数据
    
5. 更新数据内容 → 删除测试数据
    
6. 销毁集合 → 销毁数据库
    
7. 全程自动清理，无残留测试数据
    

### 2. 异常处理

代码已添加全局异常捕获，运行失败会输出清晰错误信息：

```python
if __name__ == "__main__":
    try:
        mongodb_crud_demo()
    except Exception as e:
        print(f"❌ 运行失败: {e}")
```

---

## 四、生产环境最佳实践

1. **连接安全**
    
    1. 生产环境必须开启用户名密码认证
        
    2. 连接 URI 使用环境变量 / 配置文件管理，禁止硬编码
        
2. **性能优化**
    
    1. 批量操作优先使用 `insert_many`/`update_many`
        
    2. 查询必须加索引，禁止全表扫描
        
    3. 使用 `limit()` 限制返回数据量
        
3. **数据规范**
    
    1. 统一字段命名（驼峰 / 下划线）
        
    2. 必加 `created_at`/`updated_at` 时间字段
        
    3. 重要字段做非空校验
        
4. **操作安全**
    
    1. 删除操作必须二次确认
        
    2. 生产环境禁止直接使用 `drop_database`
        

---

## 五、常用扩展功能

### 1. 带条件分页查询

```python
# 查询author=Gemini-CLI，按时间倒序，取前10条
collection.find({"author": "Gemini-CLI"}).sort("created_at", -1).limit(10)
```

### 2. 创建索引（加速查询）

```python
# 为author字段创建单字段索引
collection.create_index("author")
# 复合索引
collection.create_index([("author", 1), ("created_at", -1)])
```

### 3. 检查文档是否存在

```python
def is_doc_exists(collection, _id):
    return collection.count_documents({"_id": _id}) > 0
```

### 4. 字段筛选（仅返回需要的字段）

```python
# 只返回title和content字段，屏蔽_id
collection.find_one({"_id": document_id}, {"_id": 0, "title": 1, "content": 1})
```

---

## 六、MongoDB 与 传统 SQL 对应关系

|   |   |
|---|---|
|MongoDB 概念|传统 SQL 概念|
|数据库（Database）|数据库（Database）|
|集合（Collection）|表（Table）|
|文档（Document）|行（Row）|
|字段（Field）|列（Column）|
|_id|主键（Primary Key）|
|find()|SELECT|
|insert_one()|INSERT|
|update_one()|UPDATE|
|delete_one()|DELETE|

---
