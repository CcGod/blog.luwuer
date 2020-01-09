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