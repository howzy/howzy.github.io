---
title: Git 实战手册
date: 2020-01-14 17:40:25
tags: 
  - Git
---

## 一、前言

先来说说 Git 最本质的工作流程吧，从远程仓库拉取代码到本地版本库，签出分支然后在本地工作区进行开发，通过暂存区将代码提交到版本库，最后再推送到远程仓库。这样的流程不难理解，但是在实际团队协作中，往往会涉及到自己的分支、其他成员的分支、公共分支等分支的合并、解决冲突、推送、回退之类的操作。为了梳理清楚 Git 的各种操作，本文将以 场景 + 解决方案 的形式组装成操作手册，便于自己日后查阅，以及团队新成员能快速上手。

## 二、必备知识点

Git 的核心概念分为四个区块，分别是远程仓库、本地版本库、暂存区以及工作区，下面这张图能清晰的描述出这四个区块之间的关系。

![Git 核心区块](/img/git_reset_20170826.png)

<!-- more -->

>全局说明：分支后的星号*代表的是当前分支。

## 三、拉取篇

**场景 1.** 假设现有两个远程分支 master、branchA，并已经拉取到本地版本库，此时一同事删除了远程仓库中的分支 branchA，但是你电脑上仍然能看到 origin/branchA ，怎样才能更新仓库呢？

**解决方法：** 获取远程仓库的新分支以及删除远程仓库已删除的分支（本地版本库中的分支）

```
git fetch -p  // --prune(修剪)的缩写
```

**场景 2.** 本地新增提交记录后，准备推到远程前会先从远程 `git pull` 拉取代码进行更新，假设此时其他同事已经推送新的代码，为什么这时候拉取会自动产生一个（看似）没有意义的提交记录，怎样让提交记录更直观明了？

原始提交记录

```
      C---D  origin/branchA
     /
A---B---E  branchA*
```

git pull 之后的提交记录多了一个 `Merge branch "XXX" into XXX`，也就是下例的 F 点

```
      C---D  origin/branchA
     /     \
A---B---E---F  branchA*
```

**解决方法：**

```
git pull --rebase
```

```
      origin/branchA
            |
A---B---C---D---E  branchA*
```

>git pull = git fetch + git merge // 拉取到本地版本库 + 合并到本地工作区
>git pull --rebase = git fetch + git rebase // 拉取到本地版本库 + 工作区变基到最新版本库

## **四、分支篇**

**场景 1.** 怎么从指定分支创建分支?

**解决方法：**

```
// 从目标分支创建本地分支，如果没有指定目标分支，默认为当前分支
git branch <name> [<targetBranch>]

// 创建并切换到该分支
git checkout -b <branchname> [<tragetBranch>]
```

**场景 2.** 怎么将本地分支关联到指定的远程分支?

**解决方法：**

```
git branch --set-upstream-to=<origin/branchname>
// 或者
git branch -u <origin/branchname>
```

**场景 3.** 怎么取消本地分支和远程分支的关联?

**解决方法：**

```
git branch --unset-upstream [<origin/branchname>]
```

**场景 4.** 怎么查看分支以及其关联的远程分支的详细信息?

**解决方法：**

```
git branch -vv
```

**场景 5.** 怎么删除本地分支？

**解决方法：**

```
git branch -D <branchname>
```

**场景 6.** 怎么删除远程分支？

**解决方法：**

```
git push -d origin <branchname>
// 或者
git push origin :branchname
```

**场景 7.** 怎么移动/重命名分支？

**解决方法：**

```
// 将目标分支 移动/重命名 到新分支
git branch -m <branchname> [<targetBranch>]
```

**场景 8.** 怎么合并分支？

**解决方法：**`merge` 合并

```
git merge <branchname>
```

情况1：原始提交记录

```
      C---D  feature
     /
A---B  master*
```

情况1：合并之后的提交记录

```
A---B---C---D  master*、feature
```

情况2：原始提交记录

```
      C---D  feature
     /
A---B---E  master*
```

情况2：合并之后的提交记录

```
      C---D  feature
     /     \
A---B---E---F master*
```

> 在多人协作开发场景下，merge 操作容易产生分支间的闭环，使得分支提交信息变得不清晰，不利于分支维护。


**场景 9.** 怎么合并指定分支的指定提交记录？

**解决方法：**`cherry-pick` 遴选

```
// 合并指定提交记录
git cherry-pick <commit>

// 合并多个提交记录
git cherry-pick <commit1> <commit2> <commit3>

// 若提交记录在同一分支上，则可以采用区间形式(start, end]，commit1不包含，commit3包含
// 等价于合并了 commit2、commit3
git cherry-pick <commit1>..<commit3>

// 若省略了区间形式的起点，则起点默认为两个分支的交点
git cherry-pick ..<commit3>
```

原始提交记录

```
      C---D  feature
     /
A---B---E  master*
```

`git cherry-pick B..D` 遴选之后的提交记录

```
      C---D  feature
     /
A---B---E---C'---D'  master*
```

> cherry-pick 操作会拷贝提交记录到当前分支，使得当前分支的提交信息更为清晰。

扩展：`cherry-pick` 操作若产生了冲突，处理流程如下

1. 解决冲突，或者想中断此次操作 `git cherry-pick --abort`（中断则不需要后两步操作）
2. 添加到暂存区 `git add .`
3. 继续遴选 `git cherry-pick --continue`

**场景 10.** 从公共分支签出特性分支进行新功能开发，开发完后准备合并回功公共分支，此时发现公共分支已经有其他人推送新的提交记录，此时怎么以公共分支来更新特性分支？

**解决方法：**通过 `rebase` 变基，将当前分支的基点移动到目标分支上

```
// branchname 默认为当前分支，可省略
git rebase <targetBranch> [<branchname>]

// 等价于下面两条命令
git checkout <branchname>
git rebase <targetBranch>
```

原始提交记录

```
      C---D  feature
     /
A---B---E  master*
```

`git rebase master` 变基之后的提交记录

```
          C'---D'  feature*
         /
A---B---E  master
```

> 变基操作能更新当前分支的基点（起点），使当前分支包含公共分支上新的提交记录，这样有个好处就是如果和主分支存在冲突，可以在特性分支上提前解决。
>
> 因为重塑了历史提交记录，所以变基后当前分支会和远程分支不一致，需要采用强制推送（参见下文）覆盖远程分支。

扩展：`rebase` 操作若产生了冲突，处理流程如下

1. 解决冲突，或者想中断此次操作 `git rebase --abort`（中断则不需要后两步操作）
2. 添加到暂存区 `git add .`
3. 继续变基 `git rebase --continue`

**场景 11.** 怎么合并多个提交记录？

**解决方法：**`git rebase --interactive` 或者缩写形式 `git rebase -i`交互式变基

```
// commit 为需要处理的提交记录区间的父节点
git rebase -i <commit>
```

原始提交记录

```
A---B---C---D feature*
```

需要合并 C、D 两个提交记录，`git rebase -i B`

```
pick a0a6eba feat: 新增 C 功能
pick 95f09e5 feat: 新增 D 功能
```

将 D 提交记录合并进 C 中 ，所以把 D 记录的 `pick` 改为 `squash`，然后保存退出即可

```
pick a0a6eba feat: 新增 C 功能
squash 95f09e5 feat: 新增 D 功能
```

最终的提交记录，E 点则包含了 C 和 D 的全部改动

```
A---B---E feature*
```

扩展：交互式变基过程中会自动进入 vi 编辑模式，参数解释如下

1. p，pick 选中
2. r，reword 选中，并且修改提交信息
3. e，edit 选中，rebase 时会暂停，允许你修改这个 commit
4. s，squash 选中，会将当前 commit 与上一个 commit 合并
5. f，fixup ，与 squash 相同，但不会保存当前 commit 的提交信息
6. x，exec 执行其他 shell 命令

## **五、提交篇**

**场景 1.** 最近的一次提交记录信息错误怎么修改？

**解决方法：**

```
git commit --amend -m '新的提交信息'
```

**场景 2.** 最近的一次提交，发现遗漏了部分改动？

**解决方法：**出于提交信息的完整性考虑，在不新增提交记录的情况下将遗漏的改动合并到本次提交中。

```
// 将遗漏的文件改动添加到暂存区
git add <filename>

// 将暂存区中的所有文件合并到最近一次提交中，
// 如果不带 --no-edit 参数，则在合并之后会进入提交信息修改面板
git commit --amend --no-edit
```

## **六、推送篇**

**场景 1.** 怎么推送到远程指定分支？

**解决方法：**

```
// 推送到远程指定分支，并创建关联
git push -u origin <branchname>

// 若本地分支已经与远程分支关联，则可省略远程分支
git push
```

**场景 2.** 怎么强制推送到远程分支？

**解决方法：**

```
git push -f // --force 的缩写
```

若在强制推送的过程中，已经有其他人推送到该远程分支，则会使他人的提交记录丢失，**`为了更安全的推送`**，可用如下命令。**`在他人推送了新的提交的情况下，强制推送会被拒绝`**。

```
git push --force-with-lease
```

扩展：需要强制推送的场景可能如下

- 分支 `git rebase` 变基操作后
- 将错误代码推送到了远程，想要丢弃此提交记录（建议用下文介绍的 `git revert` 操作）

> 强制推送到远程分支会覆盖远程，若不熟悉此命令，请慎用！

## **七、撤销篇**

**场景 1.** 怎么撤销本地工作区文件的改动？

**解决方法：**

```
// 丢弃工作区某个文件的改动
git checkout -- <filePath>

// 丢弃工作区所有文件的改动
git checkout -- .
```

> 撤销本地工作的改动后，文件会回退到最近一次 commit 或 add 状态。

**场景 2.** 怎么撤销暂存区的改动？

**解决方法：**

```
// 当没有指定 filename 时，表示撤销暂存区中的所有文件
git reset HEAD <filename>
```

> 撤销暂存区的改动后，文件会回到工作区状态。

**场景 3.** 怎么撤销本地版本库的改动？

**解决方法：**

```
// 回退到指定的提交记录
git reset [<mode>] [<commit>]
```

> 通过指定 mode 参数，使回退之后的文件处于相应状态：
>
> - --soft：将当前分支重置到指定 <commit>，当前版本与指定版本间的改动文件处于 **暂存区** 中，待提交状态。
> - --hard：重置 **暂存区** 和 **工作区**，自 <commit> 以来暂存区和工作区中的任何修改都被 **丢弃**。
> - --mixed（默认）：将当前分支重置到指定 <commit>，当前版本与指定版本间的改动文件处于 **工作区** 中，**'not staged'** 状态。

扩展：HEAD 可以理解为一个指针，总是指向当前分支上最近一次的提交记录。HEAD^ 表示上一个提交记录，HEAD^^表示上两个提交记录，HEAD~n 表示上 n 个提交记录。

```
           HEAD^/HEAD~1
                |
A---B---C---D---E---F  branchA
            |       |
    HEAD^^/HEAD~2   HEAD
```

**场景 4.** 下班前急匆匆的将代码推到远程仓库，然后愉快的回家了，结果第二天发现推远程的代码出错了（手动滑稽 :p ），这时候怎么撤销远程仓库的改动？

**解决方法：**`revert` 逆向修改，然后重新提交并推送到远程仓库。

```
// 撤销修改
git revert <commit>
// 推送到远程仓库，实现远程仓库的撤销
git push
```

扩展：`revert` 和 `reset` 的区别

`revert` 是新增一次提交记录，其修改内容正好抵消指定 <commit> 的改动，而 `reset` 的撤销效果是重置了版本库。假设本地版本库和远程仓库一致，`reset` 撤销了一个提交，此时本地版本库落后远程仓库一个版本，`git push` 推送到远程会失败，而 `revert` 新增了一个提交，本地版本库领先远程仓库一个记录，此时 `git push` 可以正常推送到远程。

## **八、日志篇**

**场景 1.** 本地 commit 提交了但是未推送到远程仓库，此时 `git reset --hard` 误操作强制回滚，弄丢的提交记录怎么恢复？

**解决方法：**通过 `git reflog` 查看引用日志，找到误删的提交记录，然后回滚到这条被删除的记录

```
git reflog // Reference logs(引用日志)，能记录 HEAD 和分支引用所指向的历史
```

假设本地提交了三次 commit，然后误操作强制回滚到第一次提交，导致第二、三次的提交全部丢失

```
// git reflog 引用日志输出格式如下
1c36188 HEAD@{0}: reset: moving to 1c36188
d921970 HEAD@{1}: commit: feature-3
1c002dd HEAD@{2}: commit: feature-2
1c36188 HEAD@{3}: commit (initial): feature-1
```

找到误删的提交记录 feature-2 和 feature-3

```
git reset --hard d921970
```

## **九、贮藏篇**

**场景 1.** 当正在编写代码的时候，接到了一个紧急任务，需要切换分支去开发，此时未完成的代码怎么保存？

**解决方法：**

```
// 将全部未保存的代码添加到贮藏区，若未填写描述信息，则以上一次 commit 的信息记录
git stash [push [-m <message>]]
```

**场景 2.** 怎么查看贮藏区中保存的代码？

**解决方法：**

```
git stash list

// 若存在贮藏的代码，则输出格式如下
stash@{0}: On feature: 新功能开发未完成，先贮藏一下       // 添加了描述信息
stash@{1}: WIP on master: 8e50dc3a feat：添加新功能xxx  // 未填写描述信息
```

**场景 3.** 怎么取出贮藏的代码？

**解决方法：**

```
// 恢复指定下标的贮藏代码，并删除对应的贮藏列表，index 默认为 0
git stash pop [index]

// 等价于下面两条命令
git stash apply [index] // 取出贮藏
git stash drop [index] // 删除贮藏列表
```

**场景 4.** 怎么清空贮藏列表？

**解决方法：**

```
git stash clear
```
