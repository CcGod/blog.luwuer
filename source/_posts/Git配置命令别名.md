---
title: Git命令配置别名
tags: git
---

##Git配置命令别名

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