# Ollama 本地 LLM 使用文档

## 简介

Ollama 是一个在本地运行大语言模型的工具，支持各种开源模型（如 Llama 3, Mistral, Qwen 等）。

## 安装

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

> ⚠️ 需要翻墙访问 Ollama 官方安装脚本

## 基础命令

### 查看已下载的模型

```bash
ollama list
```

### 运行模型

```bash
# 基础运行
ollama run <模型名称>

# 指定参数运行
ollama run <模型名称> "你的问题"

# 指定模型和参数
ollama run <模型名称> -p "提示词"
```

### 自定义模型

```bash
# 查看可用模型
ollama show <模型名称>

# 导出模型
ollama save <模型名称> <文件路径>

# 导入模型
ollama import <文件路径>
```

## 常见模型

- `llama3`: Meta Llama 3 (推荐)
- `mistral`: Mistral AI
- `qwen`: 阿里通义千问
- `gemma`: Google Gemma

## 端口配置

默认运行在 `http://localhost:11434`

## API 使用

Ollama 提供完整的 REST API：

```bash
# 列出模型
curl http://localhost:11434/api/tags

# 运行推理
curl http://localhost:11434/api/generate -d '{
  "model": "llama3",
  "prompt": "你的问题"
}'

# 对话
curl http://localhost:11434/api/chat -d '{
  "model": "llama3",
  "messages": [{"role": "user", "content": "你的问题"}]
}'
```

## 配置文件

配置文件位置：
- Linux/macOS: `~/.ollama/`
- Windows: `%APPDATA%\Ollama\`

## 性能优化

- 使用 GPU 加速（NVIDIA CUDA）
- 根据硬件调整上下文长度
- 选择合适的模型大小（7B/13B/70B）

## 故障排查

### 常见问题

1. **安装失败** → 检查网络连接
2. **模型下载慢** → 使用代理或手动下载
3. **内存不足** → 使用较小的模型

### 日志查看

```bash
# Linux/macOS
tail -f ~/.ollama/logs/server.log

# Windows
Get-Content "$env:APPDATA\Ollama\logs\server.log" -Wait
```

## 参考资源

- [Ollama 官方文档](https://ollama.com/docs)
- [模型库](https://ollama.com/library)