
## 安装UV

![安装](uv.md#安装)

## 在项目中初始化环境

1. UV初始化
```
uv init
uv python pin 3.11
```
2. 修改uv 项目的pyproject.toml文件
```toml
[project]
name = "rsai-gui"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.11, <3.12"
dependencies = [
    "einops>=0.8.1",
    "gdal",
    "nuitka>=2.8.9",
    "opencv-python>=4.13.0.90",
    "pyqt5==5.15.11",
    "pyqt5-qt5==5.15.2",
    "pyqt5-sip==12.17.2",
    "rasterio>=1.4.4",
    "scikit-image>=0.26.0",
    "scipy>=1.17.0",
    "timm>=1.0.24",
    "torch>=2.9.1",
    "torchvision>=0.24.1",
    "tqdm>=4.67.1",
]

[[tool.uv.index]]
name = "pytorch"
url = "https://download.pytorch.org/whl/cu130" #根据对应的cuda版本选择后缀
explicit = true

[tool.uv.sources]
torch = { index = "pytorch" }
torchvision = { index = "pytorch" }
gdal = { path = "C:/Users/ljx/Downloads/gdal-3.11.4-cp311-cp311-win_amd64.whl" }
# 手动下载的gdal包路径
```
