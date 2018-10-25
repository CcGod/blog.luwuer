---
title: Linux yum 安装包默认目录
tags: [linux, yum]
date: 2017-09-27 17:40:50
---

> **原文[链接](https://www.cnblogs.com/kerrycode/p/6924153.html)**

YUM（全称为 Yellow dog Updater, Modified）是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，避免了手动安装的麻烦(寻找资源、下载；放到指定目录安装；处理依赖关系并下载依赖关系的包进行安装)。所以用yum安装，实质上是用RPM安装，所以RPM查询信息的指令都可用。

一般来说，RPM默认安装路径如下:
| 目录 | 目录主要内容 |
| --- | --- |
| /etc | RPM包配置文件位置 |
| /usr/bin | RPM包可执行文件文件位置 |
| /usr/share/doc | RPM包本的软件使用手册与帮助文档位置 |

> 一般用`rpm -qa|grep nginx`获得完整包名，再用`rpm -ql nginx-1.12.2-2.el7.x86_64`查看nginx包位置