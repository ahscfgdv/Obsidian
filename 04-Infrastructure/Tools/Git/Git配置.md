```bash

git config --global user.name "你的名字"

git config --global user.email "你的邮箱"

git config --list

git config --global core.editor "vim" 
# 使用 vim 作为默认编辑器
git config --local commit.template .gitmessage
# 配置git提交信息模板
```

## git commit 模板

```bash
# <type>(<scope>): <subject>
#
# <body>
#
# <footer>
#
# ----------------------------------------------------------------------
# 填写说明：
#
# 1. <type> (必填) - 提交类型:
#    feat:     新功能 (A new feature)
#    fix:      修复 Bug (A bug fix)
#    docs:     仅文档更改 (Documentation only changes)
#    style:    代码格式调整，不影响逻辑 (white-space, formatting, missing semi-colons, etc)
#    refactor: 代码重构，既不修 Bug 也不加新功能 (A code change that neither fixes a bug nor adds a feature)
#    perf:     性能优化 (A code change that improves performance)
#    test:     测试相关 (Adding missing tests or correcting existing tests)
#    build:    构建系统或依赖更新 (npm, pip, maven, gradle etc)
#    ci:       CI 配置更改 (k8s, docker, github actions etc)
#    chore:    杂项，构建过程或辅助工具的变动
#    revert:   回滚代码
#
# 2. <scope> (可选) - 影响范围:
#    例如: auth, rag, api, db, config 等。
#
# 3. <subject> (必填) - 简短描述:
#    - 使用祈使句，现在时 (例如 "add" 而不是 "added")
#    - 首字母小写 (英文规范)
#    - 结尾不加句号
#    - 不超过 50 个字符
#
# 4. <body> (可选) - 详细描述:
#    - 解释修改的原因 (Motivation)
#    - 解释与之前行为的差异 (Contrast with previous behavior)
#    - 每行约 72 个字符换行
#
# 5. <footer> (可选) - 备注:
#    - BREAKING CHANGE: 重大变更说明
#    - 关联 Issues (例如: Closes #123, Fixes #456)
# ----------------------------------------------------------------------
```

