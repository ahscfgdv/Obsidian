
## python导入父目录中的包

解决方法：将父目录加入sys.path

```python

import sys

from pathlib import Path

# 获取当前文件的祖父目录（即父文件夹）

path_root = Path(__file__).parents[1]

# 添加到 sys.path

print("path_root:", path_root)

sys.path.append(str(path_root))

from test import test_gui

```

由于test包在其他的环境变量中存在，