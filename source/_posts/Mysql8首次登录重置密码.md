---
title: Mysql8首次登录重置密码
tags: [mysql]
date: 2017-09-25 17:40:50
---

1. 登录mysql
    ```bash
    [root@VM_135_188_centos log]# mysql -uroot -p
    Enter password: 
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 9
    Server version: 8.0.12
    ...
    ```
2. 改密码报错: `Your password does not satisfy the current policy requirements`
    ```bash
    mysql> set password for root@localhost = '123456';
    ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
    ```
3. 修改密码等级 + 修改密码
   ```bash
    mysql> set global validate_password.policy=0; # 0 LOW 1 MEDIUM 2 STRONG
    Query OK, 0 rows affected (0.00 sec)

    mysql> set global validate_password.length=6;
    Query OK, 0 rows affected (0.00 sec)

    mysql> ALTER USER root@localhost IDENTIFIED BY '123456';
    Query OK, 0 rows affected (0.11 sec)
   ```
4. 查看密码验证相关（需要改了密码重新登录）
    ```bash
    mysql> show global variables like '%validate_password%';
    +--------------------------------------+-------+
    | Variable_name                        | Value |
    +--------------------------------------+-------+
    | validate_password.check_user_name    | ON    |
    | validate_password.dictionary_file    |       |
    | validate_password.length             | 6     |
    | validate_password.mixed_case_count   | 1     |
    | validate_password.number_count       | 1     |
    | validate_password.policy             | LOW   |
    | validate_password.special_char_count | 1     |
    +--------------------------------------+-------+
    7 rows in set (0.01 sec)

    ```

> 注意是`validate_password.*`而不是以前的`validate_password_*`