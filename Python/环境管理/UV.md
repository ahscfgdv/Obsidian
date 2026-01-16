
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