---
title: 遗忘笔记之shadowsocks
tags: [遗忘笔记, linux, shadowsocks]
date: 2017-09-27 17:40:50
---

#### shadowsocks重启
1. `ps -ef|grep shadowsocks` 查看进程和配置文件路径
2. `kill -quit 823 ` 杀死已存在进程
3. `ss-server -c /etc/shadowsocks-libev/config.json start`启动进程（restart重启）

> shadowsocks可执行文件在`/usr/local/bin/ss-server`，如果报错`ss-server: command not found`就去这里执行第三部的命令；另很多教程在start前面加上一个-d会导致nameservers报错`failed to set nameservers`，因为加上-d shadowsocks会默认去`/etc/resolv.conf`里边取值，而不是你shadowsocks的配置文件。