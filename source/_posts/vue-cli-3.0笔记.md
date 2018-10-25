---
title: vue-cli-3.0笔记
tags: [vue, vue-cli]
date: 2017-09-19 17:40:50
---

### 核心概念

- CLI 插件：每个 CLI 插件都会包含一个 (用来创建文件的) 生成器和一个 (用来调整 webpack 核心配置和注入命令的) 运行时插件。附： [插件开发指南](https://cli.vuejs.org/zh/dev-guide/plugin-dev.html#%E6%A0%B8%E5%BF%83%E6%A6%82%E5%BF%B5)

- abc

### 命令简介

1. `yarn global add @vue/cli` 安装vue/cli

2. `vue create hello-world` 创建hello-world项目

    ```bash
      -p, --preset <presetName>       忽略提示符并使用已保存的或远程的预设选项
      -d, --default                   忽略提示符并使用默认预设选项
      -i, --inlinePreset <json>       忽略提示符并使用内联的 JSON 字符串预设选项
      -m, --packageManager <command>  在安装依赖时使用指定的 npm 客户端
      -r, --registry <url>            在安装依赖时使用指定的 npm registry (仅用于 npm 客户端)
      -g, --git [message]             强制 / 跳过 git 初始化，并可选的指定初始化提交信息
      -f, --force                     覆写目标目录可能存在的配置
      -c, --clone                     使用 git clone 获取远程预设选项
      -x, --proxy                     使用指定的代理创建项目
      -h, --help                      输出使用帮助信息
    ````

3. `vue ui` 打开图形化界面创建和管理项目

3. `vue add @vue/eslint` 为已创建项目安装插件,这个命令将 @vue/eslint 解析为完整的包名 @vue/cli-plugin-eslint，然后从 npm 安装它，调用它的生成器。
    > vue add 的设计意图是为了安装和调用 Vue CLI 插件。这不意味着替换掉普通的 npm 包。对于这些普通的 npm 包，你仍然需要选用包管理器。

    > 我们推荐在运行 vue add 之前将项目的最新状态提交，因为该命令可能调用插件的文件生成器并很有可能更改你现有的文件。

