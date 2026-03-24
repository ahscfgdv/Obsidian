可以调用工具自动完成编程

## 管理文件

使用`@`直接读取指定文件或文件夹

如果不指定可能会读取整个项目浪费Token

创建geminiignore可以让Gemini Cli忽略指定文件，Gemini Cli也不会读取被gitignore忽视的文件


## 管理上下文和内存

通过`Gemini.md`实现长期记忆，在`Gemini.md`中要使用确定的语句如**不要**

1. **Global:** `~/.gemini/GEMINI.md` (Rules for _every_ project you work on).  
    **全球：**`~/.gemini/GEMINI.md`（适用于您工作的每个项目的规则）。
2. **Project Root:** `./GEMINI.md` (Rules for the current repository).  
    **项目根：**`./GEMINI.md`（当前存储库的规则）。
3. **Subdirectory:** `./src/GEMINI.md` (Rules specific to the `src` folder).  
    **子目录：**`./src/GEMINI.md`（`src` 文件夹特定的规则）。

## 管理会话和历史记录

gemini -r 恢复上一场对话

**删除会话**

gemini --list-sessions
gemini --delete-session 1



## 设置Gemini Cli使用gemini3

```

/settings

Preview Features (e.g., models) = true

/model

选择gemini3
```

## 使用Gemini.md

当修改了Gemini.md时使用
`/memory refresh`

## 换行

输入`\`之后回车 

## 命令

### /compress

**说明**：将整个聊天上下文替换为摘要，节省 token，同时保留内容概要。