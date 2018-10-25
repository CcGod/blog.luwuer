---
title: Navicat连接MySql 8报错：Client does not support authentication protocol requested by server
tags: [MySql]
date: 2017-09-19 17:40:50
---

Navicat连接新装Mysql报错： `Client does not support authentication protocol requested by server...`

解决办法：

- 进入安装目录/bin
  ```bash 
  cd %PATH%\bin # %PATH% Mysql安装目录
  ```

- 登录mysql

  ```bash
  # 登录
  mysql -u root -p 
  # 输入密码
  Enter password: ************ 
  ```

- 修改密码及密码加密方式

  ```bash
  # 修改密码及密码加密方式
  ALTER USER root@localhost IDENTIFIED WITH mysql_native_password BY '123456'; 
  # 刷新MySQL的系统权限相关表
  FLUSH PRIVILEGES; 
  ```
