---
name: doc-summarizer
description: 自动检索库文档并总结核心内容。当用户需要深入了解某个第三方库、框架或工具的 API 核心、配置选项或架构设计并生成 Markdown 总结时使用。
---

# 文档总结专家 (Doc Summarizer)

该技能旨在利用 `context7` 提供的实时文档库，快速提取任何编程库或框架的核心价值，并输出为高质量的 Markdown 文件。

## 执行流程

1. **识别目标**：从用户输入中提取库或框架的名称（例如：`FastAPI`, `TailwindCSS`）。
2. **解析 ID**：调用 `mcp_context7_resolve-library-id` 获取官方或高信誉度的 Library ID。
3. **深度检索**：使用 `mcp_context7_query-docs` 分别查询以下核心维度：
   - **快速开始 (Quick Start)**：核心安装与最小运行示例。
   - **核心概念 (Core Concepts)**：架构模型、生命周期或核心对象。
   - **关键 API/配置 (API Reference)**：最常用的函数、类或配置参数。
   - **最佳实践 (Best Practices)**：官方建议的模式或常见陷阱。
4. **生成总结**：按以下 Markdown 模板输出内容：

---

# [库名称] 核心指南

> 快速概览由 Gemini CLI `doc-summarizer` 自动生成。

## 1. 简介与核心价值
[简短描述其主要用途]

## 2. 快速起步
```bash
[安装命令]
```
```python/js/...
[最小运行代码示例]
```

## 3. 核心 API 与配置
| 名称 | 类型 | 描述 |
| :--- | :--- | :--- |
| [Symbol] | [Type] | [Brief Description] |

## 4. 关键概念
- **[概念 A]**: [描述]
- **[概念 B]**: [描述]

## 5. 进阶与最佳实践
- [建议 1]
- [建议 2]

---
*数据来源: Context7 [Library ID]*

## 注意事项
- 优先选择 **Source Reputation: High** 且 **Benchmark Score** 较高的库 ID。
- 如果库版本非常关键，请在查询时指定版本（如 `/vercel/next.js/v14`）。
- 始终在总结末尾注明数据来源，确保可追溯性。
