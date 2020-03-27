---
title: Git使用笔记
tags: git
---

## 设置命令别名

- `git config --global -e`进入config设置
- 在alias下面添加别名规则：
    ```
    [alias]
        co = checkout
        st = status
        br = branch
        mg = merge
        ci = commit
        cf = config
    ```

## 临时存储代码
```bash
# 把当前工作区暂存和未暂存代码缓存
git stash

# 查看缓存列表
git stash list
# stash@{0}: WIP ...
# stash@{1}: WIP ...

# 应用缓存
git stash apply stash@{0}

# 删除缓存
git stash drop stash@{0}

# 应用缓存并删除该条缓存
git stash pop stash@{0}
```

## 修改最后一次提交

如果你发现上一次提交遗漏了某个文件，或者修改未完毕就提交了，总之是想要对上一次提交内容和提交信息做修改，那么就用 `commit` 的 `--amend` 选项吧。

```bash
git commit --amend -m 'new commit message will cover the old one'
```

## 合并多个提交信息

如果你在开发中习惯用 commit 保存修改信息，导致一个功能被分成多次更小粒度的提交，那么推荐在向远程仓库 push 前把提交信息合并。

这里假设你希望把最近三条提交合并一条，并修改提交信息为"commit message":

```bash
# 用 -i HEAD^3 来指定最近三条提交
git rebase -i HEAD^3
```
然后你会看到如下信息：
```bash
pick f7f3f6d commit message 1
pick 310154e commit message 2
pick a5f4a0d commit message 3
```
修改为如下并保存：
```bash
pick f7f3f6d commit message
squash 310154e commit message 2
squash a5f4a0d commit message 3
```

## 日常提交前变基

如果团队没有明确要求只能使用 `merge` 合并代码，那么建议你的每一次 `push` 到远程仓库前，都通过 `rebase` 确保自己的改动永远置于最后，让 assignee 在合并分支时尽可能少的遇到冲突，避免 merge request 被关闭。

```bash
# 0. 提交你的修改
git add .
git commit -m '<commit message>'

# 1. before push
git pull --rebase origin dev

# 2. 解决冲突（如果有）

# 3. 将修改好的冲突添加到暂存区
git add .

# 4. 完成变基
git rebase --continue
```