# Gemini CLI 深度使用指南

Gemini CLI 是一款专为软件工程师打造的交互式 AI 助手，深度集成开发环境，通过自动化工具调用（Tool Calling）实现高效的代码编写、重构与系统管理。

## 1. 上下文与规则管理 (Context & Rules)

### 精确上下文控制
*   **资源引用 (`@`)**：在对话中使用 `@path/to/file` 或 `@directory` 直接引入特定文件或目录内容。
*   **忽略规则**：
    *   `.geminiignore`：自定义忽略规则，防止不必要的文件进入 AI 上下文。
    *   `.gitignore`：默认遵循 Git 忽略规则。
    *   可在 `settings.json` 中通过 `context.fileFiltering` 调整。

### 行为准则 (Custom Instructions)
通过编写规则文件设定 AI 的行为准则（如编码风格、架构模式）：
*   **文件命名**：默认识别 `GEMINI.md` 和 `CONTEXT.md`。
*   **层级结构**：
    1.  **全局级** (`~/.gemini/GEMINI.md`)：通用开发偏好。
    2.  **项目级** (`./GEMINI.md`)：仓库特定规范。
    3.  **目录级** (`./src/GEMINI.md`)：针对特定模块的规则。
*   **记忆刷新**：修改规则后使用 `/memory refresh` 同步。

## 2. 核心模式与执行 (Execution Modes)

### 计划模式 (Plan Mode)
解决复杂任务的首选模式。AI 会先制定详尽计划并获取用户确认后再执行。
*   **启动**：通过 `enter_plan_mode` 触发或在指令中明确要求。
*   **查看/复制**：使用 `/plan` 查看当前计划，`/plan copy` 复制到剪贴板。
*   **非交互执行**：`gemini --approval-mode plan -p "任务描述"` 自动处理所有步骤。

### 无头模式 (Headless)
适用于脚本自动化或 CI/CD。
*   **管道操作**：`cat error.log | gemini -p "分析错误原因"`。

### 沙盒环境 (Sandbox)
*   **隔离执行**：在 `settings.json` 中配置 `"tools": { "sandbox": "docker" }`，在容器中安全运行生成的脚本。

## 3. 配置系统 (Settings)

配置文件位于 `~/.gemini/settings.json` (用户级) 或 `.gemini/settings.json` (项目级)。

```json
{
  "general": {
    "vimMode": true,
    "checkpointing": { "enabled": true }, // 自动创建会话快照
    "defaultApprovalMode": "default"      // "default", "auto_edit", "plan"
  },
  "model": {
    "name": "gemini-3-pro-preview",
    "compressionThreshold": 0.5          // 上下文压缩阈值
  },
  "tools": {
    "useRipgrep": true,                   // 使用 rg 提高搜索效率
    "exclude": ["run_shell_command(rm)"]  // 禁用高危命令
  },
  "experimental": {
    "plan": true,
    "jitContext": true                    // 即时上下文注入
  }
}
```

## 4. 会话与历史控制

*   **快照与分叉**：`/resume save [name]` 保存当前状态，方便在不同方案间切换。
*   **状态恢复**：`gemini -r` 恢复最后一次对话。
*   **历史清理**：
    *   `/rewind` (或连按两次 `Esc`)：撤销上一步。
    *   `/compress`：压缩冗余历史以节省 Token。
    *   `gemini --delete-session [ID]`：清理磁盘上的历史记录。

## 5. 高级特性

*   **MCP 服务器集成**：支持模型上下文协议 (Model Context Protocol)，可连接外部工具（如数据库、API 服务）。
*   **搜索增强 (Grounding)**：实时调用 Google Search 校验技术文档，避免 AI 幻觉。
*   **自动化钩子 (Hooks)**：通过 `BeforeTool` 或 `AfterTool` 在工具执行前后触发自定义逻辑（如自动归档计划）。

## 6. 安全与最佳实践

*   **禁用 YOLO 模式**：除非完全信任，否则不要开启自动执行敏感命令。
*   **Token 优化**：优先使用 `@` 精确指向，避免盲目读取大目录。
*   **检查点**：在进行大规模代码重构前，手动触发快照。

---
*更新日期：2026年3月26日*
*文档来源：Context7 /google-gemini/gemini-cli*
