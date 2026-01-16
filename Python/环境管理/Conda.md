
## 安装

### Linux安装Minicoda

```bash

mkdir -p ~/miniconda3

wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh

bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
# -b 批处理模式 (Batch mode)，不会询问许可协议，直接同意。 
# -u 更新已存在的安装 (Update)。
# -p 指定安装路径 (Path)。

~/miniconda3/bin/conda init bash #初始化conda

conda init --reverse bash #取消conda初始化

source ~/.bashrc
```

## 更新

```bash

conda update anaconda

```

## 环境管理

```bash

conda env list 

conda activate test

conda deactivate

conda remove -n myenv --all -y

conda update -n base conda

```

## 缓存清零

```bash

conda clean --all

```