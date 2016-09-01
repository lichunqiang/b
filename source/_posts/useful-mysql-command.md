title: "经常使用到的Mysql命令"
date: 2013-11-21 23:57:23
tags: MySQL
category: [MySQL]
---

#### create database with utf-8

```
CREATE DATABASE `database_name` DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
```

#### 当没有权限访问服务器的mysql的解决办法：

```
GRANT ALL PRIVILEGES ON *.* TO root@'%' IDENTIFIED BY 'root-password' WITH GRANT OPTION;
```

#### mysql乱码：

```
$ vi /etc/my.cnf
```

添加：
[client]
default-character-set=utf8
重启mysql
```
$ service mysqld restart
```
#### mysql数据库数据导入与导出

1、导出数据库为dbname的表结构（其中用戶名为root,密码为dbpasswd,生成的脚本名为db.sql）
```
$ mysqldump -uroot -pdbpasswd -d dbname >db.sql;
```
2、导出数据库为dbname某张表(test)结构
```
$ mysqldump -uroot -pdbpasswd -d dbname test>db.sql;
```
3、导出数据库为dbname所有表结构及表数据（不加-d）
```
$ mysqldump -uroot -pdbpasswd  dbname >db.sql;
```
4、导出数据库为dbname某张表(test)结构及表数据（不加-d）
```
$ mysqldump -uroot -pdbpasswd dbname test>db.sql;
```
5、忽略某张或者多张表导出

```
$ mysqldump -uroot -pdbpasswd dbname --ignore-table=dbname.table1 --ignore-table=dbname.table2 > db.sql
```

#### 修改表名，列名

* 添加列：alter table 表名 add column 列名 varchar(30);
* 删除列：alter table 表名 drop column 列名;
* 修改列名：alter table 表名 change 旧列名 新列名;
