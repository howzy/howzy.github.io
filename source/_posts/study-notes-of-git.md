---
title: Git 学习笔记 - 基本操作
tags:
  - Git
date: 2017-08-14 17:18:29
---


### 初始化

在本地初始化一个仓库
```
git init
```

关联远程仓库，默认远程仓库名为 origin。
```
git remote add origin git@server-name:path/repo-name.git
```

<!-- more -->

推送本地分支到远程
```
git push -u origin master
```

### 分支操作

创建并切换到该分支
```
git checkout -b <name> // 创建并切换到该分支

// 等价于
git branch <name> // 创建分支
git checkout <name> // 切换分支
```

删除分支
```
git branch -d <name>

git branch -D <name> // 强制删除分支（有过提交，但是还没有合并的分支）
```

合并分支
```
git merge <name> // 采用 Fast forward 模式，直接把当前分支指向被合并分支的最新提交

git merge --no-ff -m "提交描述xxx" <name> // 禁用 Fast forward 模式，
```
> 通常，合并分支时，Git 会采用 Fast forward 模式，但在这种模式下，删除分支后，会丢掉分支信息。禁用这一模式，Git 就会在 merge 时生成一个新的 commit，这样从分支历史上就可以看出分支信息。

### 修改管理

保存修改到暂存区
```
git add <filename>
```

撤销暂存区
```
git reset HEAD <filename>
```

提交修改
```
git commit -m "提交描述xxx"
```
> -m 表示提交暂存区中的修改，-am 表示提交所有修改。

储藏当前未提交的修改
```
git stash
```
> 使用情景：当正在编写代码的时候，接到了一个紧急任务，需要切换分支去做这个紧急任务，这个时候就可以储藏一下手头未完成的工作，等处理完紧急任务后再切回分支恢复之前的工作。

查看当前储藏
```
git stash list
```

恢复储藏，并删除储藏列表
```
git stash pop // 恢复储藏，并删除储藏列表

// 等价于
git stash apply // 恢复储藏
git stash pop // 删除储藏列表
```

### 日志管理

查看提交日志
```
git log
```

查看命令日志
```
git reflog
```

### 版本控制

回退版本
```
git reset --hard HEAD^ // 回退到上一个版本

git reset --hard commit_id // 回退到指定 commit_id 的版本

```
> HEAD 表示当前版本，上个版本就是 HEAD^，上上一个版本就是 HEAD^^，或者 HEAD~2。
> 回退到指定版本这一操作可以配合日志管理一同使用，通过 git log 或者 git reflog 查看指定版本的 commit_id。

### 标签操作

创建标签
```
git tag <name> -m "说明文字"
```

删除本地标签
```
git tag -d <name>
```

推送本地标签到远程
```
git push origin --tags
```

删除远程标签
```
git push origin:refs/tags/<name>
```