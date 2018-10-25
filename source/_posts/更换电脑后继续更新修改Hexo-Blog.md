---
title: 更换电脑后继续更新修改Hexo Blog
date: 2017-08-09 19:34:54
tags: [Hexo]
---

通过Hexo搭建好Blog后,常常因为一些必须原因，需要在别的电脑更新Blog...

### 更换电脑前的操作

> 核心工作就是，备份你搭建blog后的本地环境... 我这里选择把原项目全部push到`git@github.com:xx/xx.github.io/`的分支上

- `git init`你的本地hexo blog项目，这里解释下`hexo d`只是把项目生成的静态页面发布到github~
- `git remote add origin git@github.com:xx/xx.github.io`关联本地仓库和远程仓库
- 重命名项目目录下的`.gitignore`文件，随便在后面加个下划线什么的，让他的功能（告诉git忽略一些文件或目录）不生效
- `git checkout -b branch` 新建branch分支，并切换到分支
- 打包`./themes/`目录下的主题，我用的是hexo-theme-next，故压缩这个包
  > 为什么要打包这个主题而不是在新环境中重新git clone一个新的主题呢： 主题下的.gitignore文件修改不起作用（我自己），故只有在新环境中重新git clone你用的主题的开源项目。而又因本地项目的主题中，可能存在着很多你对样式和配置的修改，新clone的主题当然什么也没有！我踩过坑，故提一下...

- `git add .` + `git commit -m 'xxx'`
- `git push -u origin branch`本地仓库内容推送到分支

**OK！ 现在你可以在另一台电脑上git clone...**

### 更换电脑后的操作

> 好累，不写注释了

```bash
git clone git@github.com:xx/xx.github.io
git checkout branch
cd xx.github.io
npm install hexo
npm install
npm install hexo-deployer-git
## 然后解压你的主题，解压到压缩包在的路径
cd .. ## 回到项目根目录
hexo clean
hexo g
hexo s
## 等等... 应该都没问题了
```