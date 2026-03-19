可以通过操纵对象的方式操作路径

---

### 1. 路径导航：像操作对象一样拼接

不再需要 `os.path.join` 这种繁琐的函数嵌套，直接用 `/` 操作符。

```python
from pathlib import Path

# 获取当前脚本所在目录的绝对路径
base_dir = Path(__file__).resolve().parent

# 向上跳两级目录，再进入 assets/images 文件夹
image_path = base_dir.parent.parent / "assets" / "images" / "logo.png"

print(image_path)
# Windows: C:\Users\Project\assets\images\logo.png
# Linux: /home/user/project/assets/images/logo.png
```

---

### 2. 快速读写：省掉 `with open`

如果你只是想简单读个配置或存个 Log，`pathlib` 提供了快捷方式。

```Python
p = Path("hello.txt")

# 写入文本 (自动处理 open/close)
p.write_text("Hello World", encoding="utf-8")

# 读取文本
content = p.read_text(encoding="utf-8")
print(content)

# 检查文件是否存在
if p.exists():
    print(f"文件大小: {p.stat().st_size} bytes")
```

---

### 3. 文件属性提取：不再用切片或 `split`

提取文件名、后缀、主文件名变得极其简单。

```Python
p = Path("/home/user/data/report.tar.gz")

print(p.name)    # report.tar.gz (文件名)
print(p.stem)    # report.tar (主文件名，注意: 只剥离最后一层后缀)
print(p.suffix)  # .gz (最后一个后缀)
print(p.suffixes)# ['.tar', '.gz'] (所有后缀列表)
print(p.parent)  # /home/user/data (父目录)
```

---

### 4. 智能创建目录：一句话搞定

以前用 `os.makedirs` 经常记不住 `exist_ok` 参数？`pathlib` 的语义更清晰。

```Python
log_dir = Path("logs/2024/march")

# parents=True: 如果父目录(logs, 2024)不存在，自动创建
# exist_ok=True: 如果目录已存在，不报错
log_dir.mkdir(parents=True, exist_ok=True)
```

---

### 5. 文件遍历与匹配：强大的 `glob`

寻找目录下特定的文件，支持递归搜索。

```Python
from pathlib import Path

current_dir = Path(".")

# 1. 遍历当前目录下所有的 .py 文件
for file in current_dir.glob("*.py"):
    print(f"发现脚本: {file.name}")

# 2. 递归遍历（包括所有子目录）所有的 .jpg 图片
for img in current_dir.rglob("*.jpg"):
    print(f"发现图片: {img.as_posix()}") # as_posix 统一转为斜杠 /
```

---
