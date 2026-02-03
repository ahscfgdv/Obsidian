
# DeclarativeBase

在 SQLAlchemy（尤其是 2.0 版本及以后）中，**`DeclarativeBase`** 是定义模型类（Models）的**基类**。它是整个“声明式映射”（Declarative Mapping）系统的核心枢纽。

以下是它的具体作用和核心功能：

## 1. 建立类与表的映射枢纽

`DeclarativeBase` 内部包含了一个 `registry`（注册表）和一个 `MetaData`（元数据对象）。

当你定义一个继承自 `DeclarativeBase` 的类时，SQLAlchemy 会自动收集这个类的所有属性（如 `Mapped` 和 `mapped_column`），并将其转化为数据库表的定义。

## 2. 核心作用分解

### (1) 管理元数据 (MetaData)

它是所有表结构的“容器”。通过 `DeclarativeBase.metadata`，你可以统一管理所有模型对应的数据库表。最常见的用途是创建表：

Python

```
# 这一行会根据所有继承了 Base 的类，在数据库中创建表
Base.metadata.create_all(engine)
```

### (2) 自动处理 `__tablename__` 与 `Table` 对象

在早期的 SQLAlchemy 中，你需要手动定义 `Table` 对象。使用 `DeclarativeBase` 后，你只需要在类中指定 `__tablename__`，它会自动在后台帮你构建 `Table` 对象，简化了代码。

### (3) 统一配置属性

你可以在自定义的 `Base` 类中定义通用的配置。例如，让所有模型都自动带上某个特定的构造函数，或者在 Python 3.7+ 中启用 `dataclasses` 集成：

Python

```
from sqlalchemy.orm import DeclarativeBase, MappedAsDataclass

class Base(MappedAsDataclass, DeclarativeBase):
    pass
    # 现在所有继承 Base 的模型都会自动具备 dataclass 的功能（如自动生成 __init__）
```

### (4) 类型检查支持 (Type Hinting)

在 2.0 版本中，`DeclarativeBase` 配合 `Mapped` 包装器，能让 IDE（如 PyCharm, VS Code）和类型检查工具（如 MyPy）完美识别模型的字段类型，从而提供极其精准的代码补全。

---

## 3. 代码示例（2.0 标准写法）

在你之前的项目中（如 FastAPI 配合 MySQL），通常会这样使用它：

Python

```
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column

# 1. 定义基类
class Base(DeclarativeBase):
    pass

# 2. 继承基类定义模型
class User(Base):
    __tablename__ = "users"
    
    # DeclarativeBase 会解析这些注解并映射到数据库
    id: Mapped[int] = mapped_column(primary_key=True)
    username: Mapped[str] = mapped_column(unique=True)
    email: Mapped[str | None]
```

## 总结

- **它是“管家”**：负责登记你定义的所有模型。
    
- **它是“翻译官”**：把你的 Python 类语法翻译成数据库能理解的 Schema。
    
- **它是“开关”**：通过它，你可以一键在数据库中创建或删除所有定义的表。
    

**如果你正在为你的 FastAPI 项目封装数据库操作，建议统一使用这种继承 `DeclarativeBase` 的方式，这比旧版的 `declarative_base()` 函数更符合现代 Python 的类型标注规范。**