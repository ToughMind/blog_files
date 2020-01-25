---
title: "mysql设置指定ip访问，用户权限相关操作"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["mysql"]
categories: ["mysql"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
# 基础语法

### `GRANT priv_type ON database.table TO user[IDENTIFIED BY [PASSWORD] 'password'] [,user [IDENTIFIED BY [PASSWORD] 'password']...]`
  
  - priv_type代表允许操作的权限。
  
  - database.table代表数据库名.表名
    - 注意\*代表所有，如database.\*代表该数据库的所有表，\*.\*代表所有数据库的所有表
  
  - user由用户名(User)和主机名(Host)构成,中间用@隔开，最好加上单引号，不加也可以执行通过。
  
  - password代表设置的密码
 
### 例子

- 授权所有数据库的所有表的所有权限给ip为任意值用户名为test密码为pwd的用户
  - `GRANT ALL ON *.* TO 'test'@'%' IDENTIFIED BY 'pwd';`

- 授权mydb数据库的所有表的增删改查权限给ip为1.1.1.1用户名为test密码为pwd的用户
  - `GRANT SELECT,INSERT,UPDATE,DELETE ON mydb.* TO 'test'@'1.1.1.1' IDENTIFIED BY 'pwd';`
 
- 授权mydb数据库的stu表的修改权限给ip为1.1.1.1用户名为test密码为pwd的用户
  - `GRANT UPDATE(name,age) ON mydb.stu TO 'test'@'1.1.1.1' IDENTIFIED BY 'pwd';`
 

# Mysql权限层级相关表
  
### user表：全局层级
- 存储用户记录的表。关键字段有Host、User、Password。

- 创建对所有表有SELECT操作权限的用户
- `GRANT SELECT ON *.* TO name@'1.1.1.1' IDENTIFIED BY 'pwd';`

### db表：数据库层级
- 存储该用户对一个数据库所有的操作权限。关键字段有Host、User、Db。 

- 授予所有权限
  - `GRANT ALL ON mydb.* TO name@'1.1.1.1' IDENTIFIED BY 'pwd';`
  
### tables_priv表：表层级

- 记录了对一个表的单独授权记录.关键字段有Host、User、Db、Table_name、Table_priv、Column_priv。

- 当授权all在某张表的时候，Table_priv会有如下授权记录：
  - Select,Insert,Update,Delete,Create,Drop,References,Index,Alter,Create View,Show view,Trigger。 
  
- 单独授权表的某一列，会记录在此表的Column_priv里
  - `GRANT UPDATE(age) ON mydb.user  TO name@'1.1.1.1';`
  
  - `GRANT SELECT(birthday) ON  mydb.user TO name@'1.1.1.1';`
  
  - 此时会在另一张表columns_priv表中留下单独授权记录
  
### columns_priv表：列层级

- 记录对表的某一列的授权记录。关键字段Host、User、Db、Table_name、Column_name。

### procs_priv表：子程序层级
- 可以对存储过程和存储函数进行权限设置。关键字段Host、User、proc_priv

# 用户相关操作

## 创建用户

### CREATE USER方式

- 必须要拥有CREATE USER权限。

- `CREATE USER user[IDENTIFIED BY [PASSWORD] 'password'],
[user[IDENTIFIED BY [PASSWORD] 'password']]...`
  - `CREATE USER 'name'@'%' IDENTIFIED BY 'pwd';`
  
### INSERT方式

- 必须拥有mysql.user表的INSERT权限。另外，ssl_cipher、x509_issuer、x509_subject等必须要设置值

- `INSERT INTO mysql.user(Host,User,Password,ssl_cipher,x509_issuer,x509_subject) VALUES('%','name',PASSWORD('pwd'),'','','') `

### GRANT方式

- 需要拥有GRANT权限。

- `GRANT priv_type ON database.table
TO user[IDENTIFIED BY [PASSWORD] 'password']
[,user [IDENTIFIED BY [PASSWORD] 'password']...]`
  - `GRANT ALL ON mydb.*  TO name@'1.1.1.1' IDENTIFIED BY 'pwd';`
  
## 删除用户

### DROP USER方式

- 需要拥有DROP USER权限。 

- DROP USER user[,user]...
  - user是需要删除的用户，由用户名(User)和主机名(Host)构成。  
  - eg：`DROP USER name@'1.1.1.1'`
  
### DELETE方式

- `DELETE FROM mydb.user WHERE Host = '% AND User = 'admin';`

## 修改用户

### 修改用户名称

- `RENAME USER name TO newname`

### 修改用户密码

### mysqladmin方式

- `mysqladmin -u username -p password "new_password";`

### 修改user表

- `UPDATE user SET Password = PASSWORD('pwd') WHERE USER = 'name';`

### SET语句方式

- 修改自己用户
  - `SET PASSWORD = PASSWORD("pwd");`

- 修改其他用户
  - `SET PASSWORD FOR 'name'@'%'=PASSWORD("pwd")`
  
### GRANT方式

- `GRANT SELECT ON *.* TO 'name'@'%' IDENTIFIED BY 'pwd'`

### 忘记密码

- mysqld_saft方式
  
  - 停止mysql：`service mysqld stop;`
  
  - 安全模式启动：`mysqld_safe –skip-grant-tables & `
  
  - 无密码回车键登录：`mysql -uroot –p
  
  - 重置密码：`use mysql; update user set password=password("") where user=’root’ and host=’localhost’; flush privileges;`
  
  - 正常启动：`service mysql restart`
  
  - 再使用mysqladmin：`mysqladmin password '123456';` 
  
- 使用普通账号来找回密码
  
  - 有一个修改test库的用户：grant all on mydb.* to test@’%’ identified by ‘test’;
  
  - 复制user表文件到test库下并且赋予mysql用户访问权限:
    - cp /home/data/mysql/data/mysql/user.* /home/data/mysql/data/test/;
   
    - chown mysql.mysql /home/data/mysql/data/test/user.*

  - mysql -utest -ptest登录修改root密码
  
  - 将test库的user表文件覆盖 mysql库的user表文件 
    - cp /home/data/mysql/data/mysql/user.* /tmp/; 
    
    - mv /home/data/mysql/data/test/user.* /home/data/mysql/data/mysql/ ;
    
    - chown mysql.mysql /home/data/mysql/data/mysql/user.*;
    
  - 查找mysql进程号，并且发送SIGHUP信号，重新加载权限表。
    - pgrep -n mysql; 
    
    - kill -SIGHUP 11111;
    
  - 无密码登录，再使用mysqladmin重新设置密码。
   
## 收回用户权限

### 查看权限
- `SHOW GRANTS;`

- `SHOW GRANTS FOR name@'%';`

- `SELECT * FROM mysql.user WHERE USER='name' AND HOST='%'; `

### revoke收回权限

- `REVOKE priv_type[(column_list)]
ON database.table
FROM user[,user]`
  - `REVOKE EXECUTE ON mydb.* FROM name@'%';`
  
# 附录常见概念

## PRIVILEGES分类权限

### 数据库/数据表/数据列权限

- Alter: 修改已存在的数据表(例如增加/删除列)和索引。

- Create: 建立新的数据库或数据表。

- Delete: 删除表的记录。

- Drop: 删除数据表或数据库。

-INDEX: 建立或删除索引。

- Insert: 增加表的记录。

- Select: 显示/搜索表的记录。

- Update: 修改表中已存在的记录。

### 全局管理MySQL用户权限：

- file: 在MySQL服务器上读写文件。

- PROCESS: 显示或杀死属于其它用户的服务线程。

- RELOAD: 重载访问控制表，刷新日志等。

- SHUTDOWN: 关闭MySQL服务。

### 特别的权限：

- ALL: 允许做任何事(和root一样)。

- USAGE: 只允许登录--其它什么也不允许做。

## user表中host列的值的意义

- %：匹配所有主机

- localhost：localhost不会被解析成IP地址，直接通过UNIXsocket连接

- 127.0.0.1：会通过TCP/IP协议连接，并且只能在本机访问；

- ::1：兼容支持ipv6的，表示同ipv4的127.0.0.1