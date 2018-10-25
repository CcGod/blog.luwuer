---
title: MySql 常用命令
tags: [MySql]
date: 2017-09-19 17:40:50
---

登录Mysql： `mysql -u root -p`

```sql
# 退出
mysql> exit

# 查看所有数据库名
mysql> show databases

# 创建一个数据库
mysql> create database @database_name

# 删除一个数据库
mysql> drop database @database_name

# 选择一个数据库操作
mysql> use @database_name

# 查看当前数据库下所有的表名
mysql> show tables

# 创建一个表
mysql> create table @table_name(uid bigint(20) not null, uname varchar(20) not null)

# 删除一个表
mysql> drop table @table_name

# SELECT @col_name FROM @table_name 检索（FROM为必须子句，以下为可选子句）
# DISTINCT 返回不同值。必须放在列名前边，不能部分使用DISTINCT
# LIMIT OFFSET 限制值位置和条数。`LIMIT 5` 限制返回五条；`LIMIT 5,5` LIMIT 5 OFFSET 5 的从第五条数据开始检索，限制五条
# ORDER BY 排序。1. 必须作为SELECT的最后一条子句；2. 多列排序；3. 位置排序：`SELECT name, phone FROM user ORDER BY 1, 2`等同于`...ORDER BY name, phone`；4. 默认正序，倒序DESC `ORDER BY name DESC, phone DESC`
mysql> SELECT phone, DISTINCT name LIMIT 2,2 FROM user ORDER BY uid DESC, 2;
```