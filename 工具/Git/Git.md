## 初始化

```bash

git config --global user.name "你的名字"

git config --global user.email "你的邮箱"

git config --list

git init

git clone <url>

```
## 分支

```bash

git branch -l #查看分支

git branch -D "branch_name" #强制删除未合并的分支

git checkout -b branch_name #创建并切换到新的分支

git checkout branch_name #切换到分支



```

## 常用

```bash

git status

git add <file>

git commit -m "提交信息"

```
## 暂存区

```bash

git reset #移除暂存区全部文件

```

## 远程

```bash

git pull

git push

git push -u origin <branch-name>

git fetch

git remote -v

```
## .gitignore

设置某些文件不经由git管理