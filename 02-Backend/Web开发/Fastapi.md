# FastAPI 核心指南

> 快速概览由 Gemini CLI `doc-summarizer` 自动生成。

FastAPI 是一个现代、高性能的 Python Web 框架，基于标准 Python 类型提示构建，具有极速编码、高性能（比肩 NodeJS 和 Go）以及自动生成交互式文档的特点。

## 1. 核心价值
- **高性能**：基于 Starlette 和 Pydantic，性能极高。
- **快速编码**：减少约 40% 的代码重复导致的错误。
- **异步支持**：原生支持 `async` 和 `await`，完美处理 I/O 密集型任务。
- **自动文档**：自动生成 Swagger UI (`/docs`) 和 ReDoc (`/redoc`)。

## 2. 快速起步

### 安装
```bash
pip install fastapi uvicorn[standard]
# 或者使用 uv
uv add fastapi uvicorn
```

### 最小运行示例
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
async def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}
```

## 3. 运行与部署 (Uvicorn)
使用 Uvicorn 作为 ASGI 服务器运行应用：
```bash
# 基础命令
uvicorn main:app --reload --port 8000 --host 0.0.0.0

# 使用 uv 运行（推荐）
uv run uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload --reload-dir app
```
- `--reload`：代码修改后自动重启（开发模式）。
- `--host 0.0.0.0`：监听所有网络接口，允许外部访问。

## 4. 参数处理
FastAPI 通过类型声明自动解析参数：
- **路径参数**：在 URL 路径中定义的变量，如 `/items/{item_id}`。
- **查询参数**：函数参数中未在路径中定义的变量，如 `?q=foo`。
- **请求体 (Request Body)**：通过 Pydantic 模型定义，自动校验 JSON 数据。

## 5. 核心特性

### 依赖注入 (Dependency Injection)
使用 `Depends` 实现逻辑复用（如认证、数据库连接）：
```python
from typing import Annotated
from fastapi import Depends, FastAPI

async def common_parameters(q: str | None = None, skip: int = 0, limit: int = 100):
    return {"q": q, "skip": skip, "limit": limit}

@app.get("/items/")
async def read_items(commons: Annotated[dict, Depends(common_parameters)]):
    return commons
```

### 中间件 (Middleware)
在请求到达路由前或响应离开后执行逻辑：
```python
@app.middleware("http")
async def add_process_time_header(request, call_next):
    start_time = time.time()
    response = await call_next(request)
    process_time = time.time() - start_time
    response.headers["X-Process-Time"] = str(process_time)
    return response
```

---
*数据来源: Context7 [/websites/fastapi_tiangolo]*
