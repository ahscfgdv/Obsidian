# Gemini CLI 深度使用指南（2026 最新版）

Gemini CLI 是一款专为软件工程师打造的交互式 AI 助手，深度集成开发环境，通过自动化工具调用（Tool Calling）实现高效的代码编写、重构与系统管理。本文档基于最新版本迭代，补充新增特性与细节优化，覆盖全场景使用范式。

## 1. 上下文与规则管理 (Context & Rules)

### 精确上下文控制

- **资源引用 (`@`)**：在对话中使用 `@path/to/file` 或 `@directory` 直接引入特定文件 / 目录内容； `@line:start-end` 语法（如 `@src/main.py:10-25`），支持精准定位文件行范围，减少无效上下文加载。
- **忽略规则**：
    
    - `.geminiignore`：自定义忽略规则，支持通配符（`*.log`、`**/node_modules`）和反向保留（`!src/utils/*.py`），防止不必要的文件进入 AI 上下文。
    - `.gitignore`：默认遵循 Git 忽略规则，优先级低于 `.geminiignore`。
    - 可在 `settings.json` 中通过 `context.fileFiltering` 调整：新增 `include` 白名单字段，支持「先忽略所有，再显式保留」的严格过滤模式。
    

### 行为准则 (Custom Instructions)

通过编写规则文件设定 AI 的行为准则（如编码风格、架构模式）：

- **文件命名**：默认识别 `GEMINI.md` 和 `CONTEXT.md`；新增支持 `gemini.rules` 后缀文件（如 `api.gemini.rules`），适配不同模块的规则隔离。
- **层级结构**：
    
    1. **全局级** (`~/.gemini/GEMINI.md`)：通用开发偏好（如代码规范、常用设计模式）。
    2. **项目级** (`./GEMINI.md`)：仓库特定规范（如工程化流程、依赖管理规则）。
    3. **目录级** (`./src/GEMINI.md`)：针对特定模块的规则（如接口命名、异常处理范式）。
    4. **临时规则**：会话内通过 `/rule add [content]` 新增临时规则，仅当前会话生效，无需修改文件。
    
- **记忆刷新**：修改规则后使用 `/memory reload` 同步；`/memory diff` 命令，对比当前规则与加载版本的差异。

## 2. 核心模式与执行 (Execution Modes)

### 计划模式 (Plan Mode)

解决复杂任务的首选模式。AI 会先制定详尽计划并获取用户确认后再执行，新版本支持计划分阶段执行与断点续跑。

- **启动**：通过 `enter_plan_mode` 触发、指令中明确要求，或新增 `gemini --plan` 命令行直接启动。
- **查看 / 复制 / 编辑**：
    
    - `/plan`：查看当前计划（新增「执行进度」标注）。
    - `/plan copy`：复制到剪贴板。
    - `/plan edit [step-id]`：直接编辑指定步骤的内容（无需重新生成全量计划）。
    
- **分阶段执行**：`/plan run --stage 1-3` 仅执行计划的 1-3 阶段，适配分步验证场景。
- **断点续跑**：计划执行中断后，`/plan resume` 从最后完成的步骤继续执行。
- **非交互执行**：`gemini --approval-mode plan -p "任务描述"` 自动处理所有步骤；新增 `--plan-output [file]` 将计划导出为 Markdown 文件。

### 无头模式 (Headless)

适用于脚本自动化或 CI/CD，新版本强化管道兼容性与输出格式化。

- **管道操作**：`cat error.log | gemini -p "分析错误原因"`；新增支持 JSON 输出：`cat data.json | gemini -p "解析数据并统计" --output json`。
- **批量任务**：`gemini --headless -f tasks.txt` 按行读取 `tasks.txt` 中的任务描述，批量执行并输出汇总报告。
- **环境变量注入**：无头模式下自动读取 `GEMINI_API_KEY`、`GEMINI_TIMEOUT` 等环境变量，适配 CI/CD 环境配置。

### 沙盒环境 (Sandbox)

强化安全隔离与多环境适配，支持本地容器、远程沙箱两种模式。

- **隔离执行**：
    
    - 本地模式：在 `settings.json` 中配置 `"tools": { "sandbox": "docker" }`，指定镜像（新增 `"sandboxImage": "python:3.11-slim"`），在容器中安全运行生成的脚本。
    - 远程模式：配置 `"sandbox": "remote"` + `"sandboxEndpoint": "https://sandbox.example.com"`，接入企业级隔离沙箱。
    
- **沙箱资源限制**：新增 `"sandboxResources": { "cpu": "1", "memory": "2G" }`，限制容器资源占用。
- **执行日志**：`/sandbox logs` 查看沙箱内命令执行日志，便于调试。

## 3. 配置系统 (Settings)

配置文件支持多层级覆盖（全局 > 项目 > 会话），新增更多精细化配置项，路径为 `~/.gemini/settings.json` (用户级)、`.gemini/settings.json` (项目级)，或会话内通过 `/config` 临时覆盖。


```json
{
  "general": {
    "vimMode": true,
    "checkpointing": { 
      "enabled": true,          // 自动创建会话快照
      "interval": 5             // 新增：每5步自动创建一次快照
    },
    "defaultApprovalMode": "default",  // "default", "auto_edit", "plan"
    "locale": "zh-CN",                 // 新增：界面/输出语言（支持 en-US/zh-CN）
    "timeout": 300                     // 新增：全局请求超时时间（秒）
  },
  "model": {
    "name": "gemini-3-pro-preview",
    "compressionThreshold": 0.5,       // 上下文压缩阈值
    "temperature": 0.2,                // 新增：生成内容的随机性（0-1）
    "maxTokens": 8192                  // 新增：单次响应最大 Token 数
  },
  "tools": {
    "useRipgrep": true,                // 使用 rg 提高搜索效率
    "exclude": ["run_shell_command(rm)"], // 禁用高危命令
    "shell": "/bin/bash",              // 新增：指定执行shell命令的终端
    "grounding": {                     // 新增：搜索增强配置
      "enabled": true,
      "searchProvider": "google",      // 支持 google/bing
      "maxSearchResults": 5            // 最多引用5条搜索结果
    }
  },
  "experimental": {
    "plan": true,
    "jitContext": true,                // 即时上下文注入
    "codeReview": true,                // 新增：代码自动评审（实验性）
    "autoFix": true                    // 新增：基于评审结果自动修复（实验性）
  }
}
```

## 4. 会话与历史控制

新增多会话管理、历史导出 / 导入，优化 Token 占用与恢复效率。

- **快照与分叉**：
    
    - `/resume save [name]` 保存当前状态，支持备注：`/resume save "重构用户模块-方案A"`。
    - `/resume list` 列出所有保存的快照，显示名称、创建时间、Token 消耗。
    - `/resume fork [name]` 基于已有快照创建新会话，适配多方案对比。
    
- **状态恢复**：
    
    - `gemini -r` 恢复最后一次对话；新增 `gemini -r [name]` 恢复指定名称的快照。
    - `/resume import [file]` 导入外部快照文件（支持 JSON 格式），跨设备同步会话。
    
- **历史清理与优化**：
    
    - `/rewind` (或连按两次 `Esc`)：撤销上一步；新增 `/rewind [n]` 撤销最近 n 步操作。
    - `/compress`：压缩冗余历史以节省 Token；新增 `--compress-level 1-3`，级别越高压缩越彻底（1 = 轻量去重，3 = 深度摘要）。
    - `gemini --delete-session [ID]`：清理磁盘上的历史记录；新增 `gemini --clean-sessions --older-than 7d` 清理 7 天前的会话。
    - `/history export [file]`：将会话历史导出为 Markdown/JSON 文件，便于归档与分享。
    

## 5. 高级特性

### MCP 服务器集成

升级模型上下文协议（Model Context Protocol v2），支持更多外部工具无缝对接：

- 原生支持数据库（MySQL/PostgreSQL）：配置 MCP 连接后，可直接在 CLI 中执行 `@db query "SELECT * FROM users LIMIT 10"` 读取数据并分析。
- API 服务集成：通过 MCP 接入 RESTful API/GraphQL，支持自动生成请求参数、解析响应结果。
- 工具链扩展：新增 MCP 插件市场，可一键安装第三方工具（如 Redis 客户端、K8s 管控工具）。

### 搜索增强 (Grounding)

实时调用搜索引擎校验技术文档，新版本支持本地文档检索与多源验证：

- 在线搜索：调用 Google Search/Bing 校验最新技术文档，避免 AI 幻觉；新增 `@search [关键词]` 手动触发搜索，结果直接融入上下文。
- 本地检索：配置 `grounding.localDocsPath` 后，可检索本地技术文档（如 `docs/` 目录下的 MD/PDF 文件），适配内网无外网场景。
- 引用标注：生成内容中自动标注搜索结果来源（链接 + 摘要），可通过 `/cite` 查看完整引用列表。

### 自动化钩子 (Hooks)

支持更多触发时机与自定义脚本，适配复杂自动化流程：

- 触发时机：新增 `BeforePlan`（计划生成前）、`AfterPlan`（计划生成后）、`OnError`（执行出错时）钩子。
- 自定义逻辑：支持执行 Shell 脚本 / Python 脚本，例如：
```json
// settings.json 配置示例
"hooks": {
  "AfterTool": "/scripts/archive-tool-log.sh",
  "OnError": "/scripts/notify-error.py --slack-webhook $SLACK_URL"
}
```
- 钩子变量：内置 `{{SESSION_ID}}`、`{{TOOL_NAME}}`、`{{ERROR_MSG}}` 等变量，便于脚本动态获取上下文。

### 代码评审与自动修复（实验性）

新增代码质量管控能力，适配研发流程闭环：

- 自动评审：`@src/main.py /review` 触发代码评审，输出规范合规性、性能、安全性等维度的报告。
- 一键修复：`/fix all` 自动修复评审中发现的问题；或 `/fix [issue-id]` 仅修复指定问题。
- 自定义评审规则：在 `GEMINI.md` 中添加 `# CodeReview` 章节，定义团队专属评审标准（如「禁止使用 eval ()」「函数行数不超过 50」）。

## 6. 安全与最佳实践

### 安全管控

- **禁用 YOLO 模式**：除非完全信任，否则不要开启自动执行敏感命令；新版本新增 `safeMode: true` 全局配置，强制要求所有 shell 命令需手动确认后执行。
- **命令权限分级**：将工具命令分为「低风险」（如 `ls`/`cat`）、「中风险」（如 `mv`/`chmod`）、「高风险」（如 `rm`/`sudo`），可在 `settings.json` 中配置不同级别权限（如高风险命令需二次确认）。
- **API 密钥保护**：禁止在会话中输出 `GEMINI_API_KEY` 等敏感信息；支持密钥通过系统密钥链（macOS Keychain/Windows Credential Manager）存储，避免明文配置。

### Token 优化

- 优先使用 `@` 精确指向（如 `@src/utils.py:5-15`），避免盲目读取大目录；新增 `/context stats` 查看当前上下文的文件列表与 Token 占用，便于精简。
- 启用 `jitContext` 即时上下文注入：仅在需要时加载文件内容，而非一次性加载所有引用资源。
- 对大文件自动分片处理：超过 1000 行的文件自动摘要，仅保留与任务相关的片段，减少无效 Token 消耗。

### 工程化最佳实践

- **检查点**：在进行大规模代码重构前，手动触发快照（`/resume save "重构前-20260326"`），便于回滚。
- **规则版本化**：将项目级 `GEMINI.md` 纳入 Git 版本管理，确保团队使用统一的 AI 行为准则。
- **CI/CD 集成**：在流水线中使用无头模式执行代码检查 / 文档生成，示例：

```bash
# GitLab CI 示例
gemini --headless -p "检查本次提交的代码是否符合团队规范" --output json > code-review.json
if jq '.hasError' code-review.json | grep true; then exit 1; fi
```

- **沙箱预验证**：生成的脚本先在沙箱中执行验证，无异常后再同步到生产环境；新增 `/sandbox test [script]` 单独测试脚本执行结果。