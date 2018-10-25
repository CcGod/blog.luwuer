---
title: Git同步本地项目到GitHub
tags: [git, github]
date: 2017-08-08 17:40:50
---

相信很多朋友都曾遇到过这样一个问题，想要在另一台电脑上同步你的个人项目，以便能身居‘异地’也依然能同步开发！所以我们把自己的项目同步到远程仓库，想在哪里里开发，就pull到本地改完再push到远程仓库，极其方便~

鉴于有好几次都记错了相关命令，故记于此，来日多多翻阅...

> 安装git和如何使用github不做讲诉...

### 准备工作，本地git获得github的提交权限
- 设置用户名和邮箱
	- `git  config --global user.name 'your_name'`
	- `git config --global user.email 'your_email'`
- 生成SSH密匙 `ssh-keygen -t rsa -C 'your_email'`,email和你设置的git邮箱一致
	> 指令执行后会让你确定密钥保存地址和设置密码，这些不用管，全部回车就好
- 添加生成的id_rsa.pub文件中的公钥（打开复制）到github的**setting / SSH AND GPG KEY / SSH keys**
- 最后测试是否关联成功,git bash输入`ssh git@github.com`, 如果提示successfully authenticated则成功

### 创建本地仓库
- cd到项目目录
- `git init` 初始化git仓库
- `git add .` 把所有项目文件添加到提交暂存区
- `git commit -m '提交说明'` 把暂存区中的内容提交到仓库

### 创建远程仓库
> 本文使用github，创建仓库步骤不赘述，仓库名（也就是项目名）`[resName]`

### 同步本地仓库到远程仓库
- `git remote add origin git@github.com:[githubUerName]/[resName]` 关联本地仓库和远程仓库
- `git push -u origin master` 把本地仓库内容push到远程仓库的master分支
    > `push`的`-u`参数是设置本地仓库默认的`upstream`,这里就是把本地仓库同远程仓库的master分支进行关联，之后你在这个仓库pull时不带参数也默认从master分支拉取

### 常见问题解决
**1.`push`时报错不能覆盖远程仓库代码**
	
事实上远程仓库是新建的，里边或许有个README.md，就再没有其他，故直接强制覆盖;`-f`:强制覆盖。
``` bash
git push -uf origin master
```

**1.`git remote add origin git@github.com:[githubUerName]/[resName]`时报错`remote origin already exists`origin别名已经存在**

删除origin，然后重试。
``` bash
git remote rm origin
```