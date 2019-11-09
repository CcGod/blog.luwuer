---
title: VSCODE 调试 Flutter 应用
date: 2019-11-09 16:02:46
tags: [VSCODE, Flutter]
---

VSCODE 调试依赖于项目根目录下的 `.vscode/launch.json` 配置文件，我们可以通过调试侧边栏的下拉框中选择“Add Configuration”来新增一个配置，或则直接编辑 `.vscode/launch.json`。

## 快速使用

1. 复制以下 Flutter 调试配置写入到你的 `.vscode/launch.json`
2. 修改 `args` 参数中的 `--local-engine` 和 `--local-engine-src-path` 为你当前环境的 engine 地址
   
```json
{
  // Use IntelliSense to learn about possible attributes.
  // Hover to view descriptions of existing attributes.
  // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Flutter",
      "request": "launch",
      "type": "dart",
      "args": [
        "--local-engine=android_debug_arm64", 
        "--local-engine-src-path=E:/flutter_tools"
      ]
    }
  ]
}
```

## launch.json 简介

- `name` 必填，配置名称，方便你在下拉框中选择
- `request` 必填，有 `launch | attach` 值可选
  - `launch` 运行程序并调试
  - `attach` 通过其他调试协议依附已运行程序进行调试
- `type` 必填，调试语言类型，比如调试 NodeJS 时填 `node`
- `program` 调试入口（文件路径）
- `args` 启动调试时的参数
- `env` 环境变量
- `cwd` 执行命令目录
- `runtimeExecutable` 使用什么命令进行调试（`node | dart | py` 等）
- `runtimeArgs` 
- `address` IP 地址 , `request: attach` 时使用
- `processId` 进程 ID , `request: attach` 时使用