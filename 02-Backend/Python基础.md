## 数据类型与变量

### 命名规范

- **变量/函数**: 小写加下划线 (`snake_case`)。
- **类**: 大驼峰 (`PascalCase`)。
- **常量**: 全大写加下划线 (`UPPER_SNAKE_CASE`)。
- **私有变量**: 
    - `_var`: 弱私有（外部仍可访问，但不建议）。
    - `__var`: 强私有（通过名称修饰 `_ClassName__var` 访问）。
- **占位符**: 单下划线 `_`。
- **避免冲突**: 不要使用 Python 内置函数（如 `list`, `str`）或模块名作为变量/文件名。

### 类型标注

```python
"""
str | None
Python3.10的新特性
不用向之前一样引入Optional语义清晰
"""
# 这里展示了参数类型注解和返回值类型注解
def find_user(user_id: int) -> str | None: 
    if user_id == 1:
        return "Alice"
    return None  # 如果没找到，返回 None
```

### 常用容器
- **List (列表)**: 有序可变序列。常用列表推导式：`[x**2 for x in range(10) if x % 2 == 0]`。
- **Dict (字典)**: 键值对映射。常用 `dict.get(key, default)` 避免 KeyDescription。
- **Set (集合)**: 无序且元素唯一。常用于去重和集合运算（交并差）。
- **Tuple (元组)**: 不可变序列。常用于函数返回多个值或作为字典键。

## 控制流

### Match-Case (Python 3.10+)

```python
def http_error(status):
    match status:
        case 400:
            return "Bad request"
        case 404:
            return "Not found"
        case 418:
            return "I'm a teapot"
        case _:
            return "Something's wrong with the internet"
```

## 函数与作用域

### 作用域 (LEGB)
- **Local**: 函数内部。
- **Enclosing**: 闭包环境（外部非全局）。
- **Global**: 模块全局。
- **Built-in**: 内置命名空间。

### 匿名函数 (Lambda)
`add = lambda x, y: x + y`

### 闭包与装饰器
装饰器是修改其他函数功能的函数。
```python
import time

def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__} executed in {time.time() - start:.4f}s")
        return result
    return wrapper

@timer
def some_task():
    time.sleep(1)
```

## 异常处理

### 自定义异常
```python
class MyProjectError(Exception):
    """项目自定义异常基类"""
    pass

class DatabaseError(MyProjectError):
    """数据库操作异常"""
    pass
```

### Raise
主动抛出异常：**raise ExceptA from ExceptB**（异常链）。

```python
try:
	payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
	user_id = payload.get("sub")
	if not user_id:
		raise HTTPException(status_code=403, detail="Invalid token")
except (JWTError, ValidationError):
	raise HTTPException(status_code=403, detail="Invalid token") from None
```

## 模块与包
- `__init__.py`: 标识目录为 Python 包。
- `__name__ == "__main__"`: 确保脚本被直接运行时才执行的代码。

## 面向对象 (OOP)

### 类与继承
```python
class BaseService:
    def __init__(self, name):
        self.name = name
    
    def log(self, msg):
        print(f"[{self.name}] {msg}")

class UserService(BaseService):
    def get_user(self, uid):
        self.log(f"Fetching user {uid}")
        return {"id": uid, "name": "Admin"}
```

### 魔术方法
- `__str__`: 返回对象的非正式字符串表示（给用户看）。
- `__repr__`: 返回对象的正式字符串表示（给开发者看/调试）。
- `__enter__ / __exit__`: 实现上下文管理器（with 语句）。

## 高级特性

### 生成器 (Generators)
使用 `yield` 关键字，节省内存，延迟计算。
```python
def count_up_to(n):
    count = 1
    while count <= n:
        yield count
        count += 1
```

### 上下文管理器 (Context Managers)
```python
from contextlib import contextmanager

@contextmanager
def temp_file():
    f = open("test.txt", "w")
    try:
        yield f
    finally:
        f.close()
```