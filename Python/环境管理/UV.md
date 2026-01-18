
https://docs.astral.sh/uv/
## 安装

* window：`powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"`
* linux：`curl -LsSf https://astral.sh/uv/install.sh | sh`
  
## 环境管理

```bash
uv venv --python 3.11 #创建虚拟环境并指定python版本
uv init server #创建一个项目
uv python pin 3.11 #固定python版本不会直接添加到虚拟环境中
uv sync #同步虚拟环境中的python版本
uv add fastapi uvicorn langchain #安装包
source .venv/bin/activate #激活虚拟环境不推荐使用

```

## 配置

```bash

uv cache dir
C:\Users\ljx\AppData\Local\uv\cache

设置环境变量
# 假设你想把缓存放在 D:\uv_cache
[System.Environment]::SetEnvironmentVariable("UV_CACHE_DIR", "D:\uv_cache", "User")



```

**项目中配置镜像源**

```toml

[tool.uv]
[[tool.uv.index]]
url = "https://pypi.tuna.tsinghua.edu.cn/simple"
default = true

```

## warning: Failed to hardlink files; falling back to full copy. This may lead to degraded performance.

