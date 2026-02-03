# command

```bash

alembic init alembic
# 初始化名为alembic的项目
```

## 项目结构

```
alembic.ini
alembic
├── env.py
├── __pycache__
│   └── env.cpython-311.pyc
├── README
├── script.py.mako
└── versions
    ├── 5b197db02e63_.py


```

### alembic.ini

- **作用**：Alembic 的主配置文件。
    
- **关键内容**：它定义了数据库的连接字符串（`sqlalchemy.url`）、日志配置、以及迁移脚本的存放路径。
    
- **注意**：在实际开发中，我们经常在 `env.py` 中动态读取环境变量，而不是把数据库密码硬编码在这个 `.ini` 文件里。

### env.py

- **作用**：这是 Alembic 迁移运行时的**入口脚本**。
    
- **核心功能**：
    
    1. 连接数据库引擎。
        
    2. **配置 `target_metadata`**：这是最关键的一步。你需要把你在 FastAPI 项目中定义的 `Base.metadata` 导入并赋值给它，这样 Alembic 才能自动检测模型变化。
        
    3. 定义如何运行迁移（“离线模式”生成 SQL 或“在线模式”直接操作数据库）。

### versions/5b197db02e63_.py

(迁移脚本)

- **作用**：每一个 `.py` 文件代表一次数据库的**版本变更**。
    
- **命名规律**：文件名中的随机字符串（如 `5b197db02e63`）是版本 ID（Revision ID）。
    
- **内部结构**：每个脚本包含两个核心函数：
    
    1. `upgrade()`：升级数据库的操作（如 `op.add_column(...)`）。
        
    2. `downgrade()`：回滚数据库的操作（删除该字段）。