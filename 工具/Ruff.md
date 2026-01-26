# Commend

```

ruff check test.py

ruff check ./

ruff format

```

# 编辑器集成Ruff

## VSCode

1. 安装Ruff扩展
2. 打开配置文件添加
```json

"editor.defaultFormatter": "charliermarsh.ruff",
"editor.formatOnSave": true,
"editor.codeActionsOnSave": { 
	"source.fixAll": "explicit", 
	"source.organizeImports": "explicit" 
},
```