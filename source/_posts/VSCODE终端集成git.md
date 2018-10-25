---
title: VSCODE终端集成git
date: 2017-08-09 21:10:26
tags: [VSCODE, git]
---

> 安装git不再赘述...

### VSCODE终端集成git


- `VSCODE/首选项/设置`或`ctrl+shift+P -> 输入:user setting`打开用户设置
- 在右侧（用户设置）添加
    ```
    // 终端在 Windows 上使用的 shell 的路径。
    "terminal.integrated.shell.windows": "[git_path]//bin//bash.exe"
    ```
- 把[git_path]修改为git安装目录，比如：`E://apps//Git//bin//bash.exe`