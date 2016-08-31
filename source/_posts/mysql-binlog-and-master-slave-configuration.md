title: "MySQL开启binlog, 配置主从记录"
date: 2016-08-31 20:50:22
tags: [MySQL]
category: [MySQL]
-------

## 开启binlog

在 `/etc/my.conf` 中打开 `log-bin=/your/dir`, 重启Mysql服务

> 根据不同的安装, MySQL的配置文件的位置可能不同, 视具体情况而定.

配置哪些数据库需要记录binlog:

```
binlog-ignore-db=mysql
binlog-ignore-db=information_schema
binlog-ignore-db=performance_schema
binlog-do-db=kit
```

`binlog-ignore-db`配置哪些数据不需要记录binlog, `binlog-do-db` 配置哪些数据需要记录binlog.

## 查看binlog状态

### 查看binlog的名称

```
mysql> show binary logs;
+---------------+-----------+
| Log_name      | File_size |
+---------------+-----------+
| binlog.000001 |       616 |
+---------------+-----------+
1 row in set (0.00 sec)
```

### 查看二进制日志的记录

```
mysql> show binlog events;
+---------------+-----+-------------+-----------+-------------+-----------------------------------------------------------------------------------------------------+
| Log_name      | Pos | Event_type  | Server_id | End_log_pos | Info                                                                                                |
+---------------+-----+-------------+-----------+-------------+-----------------------------------------------------------------------------------------------------+
| binlog.000001 |   4 | Format_desc |         1 |         120 | Server ver: 5.6.30-log, Binlog ver: 4                                                               |
| binlog.000001 | 120 | Query       |         1 |         197 | BEGIN                                                                                               |
| binlog.000001 | 197 | Query       |         1 |         317 | use `kit`; UPDATE `user` SET `username`='xxxx' WHERE `id`=5                                         |
| binlog.000001 | 317 | Xid         |         1 |         348 | COMMIT /* xid=72 */                                                                                 |
| binlog.000001 | 348 | Query       |         1 |         425 | BEGIN                                                                                               |
| binlog.000001 | 425 | Query       |         1 |         585 | use `kit`; INSERT INTO `user_profile` (`user_id`, `sex`, `last_login_ip`) VALUES (5, 1, 3232238081) |
| binlog.000001 | 585 | Xid         |         1 |         616 | COMMIT /* xid=77 */                                                                                 |
+---------------+-----+-------------+-----------+-------------+-----------------------------------------------------------------------------------------------------+
7 rows in set (0.01 sec)
```

### 导出二进制文件

```
$ mysqlbinlog --start-position=4 --stop-position=585 binlog.000001 > /home/out.txt
```

或者全部导出:

```
$ mysqlbinlog binlog.000001 > /home/out.txt
```

导出的文件名可以通过上面的`show binary logs;` 来查看需要导出的binlog文件, position 可以根据 `show binlog events`中的__Pos__字段来确定.

## 配置主从


### 查看主库状态

```
mysql> show master status;
+---------------+----------+--------------+---------------------------------------------+-------------------+
| File          | Position | Binlog_Do_DB | Binlog_Ignore_DB                            | Executed_Gtid_Set |
+---------------+----------+--------------+---------------------------------------------+-------------------+
| binlog.000003 |      120 | kit          | mysql,information_schema,performance_schema |                   |
+---------------+----------+--------------+---------------------------------------------+-------------------+
1 row in set (0.00 sec)
```


## 主从配置

### 配置主库

修改MySQL配置文件:

```
log-bin=binlog //必须开启binlog
server-id=1 // 必须, 服务器唯一ID, 默认是1,一般取IP最后一段
```

在主服务器上建立帐户并授权slave:

```
mysql > grant replication slave on *.* to slave01@192.168.10.12 identified by '123qwe';
```

重启MySQL.然后查看主库状态.


### 配置从库

修改MySQL配置文件：

```
log-bin=binlog //不是必须
server-id=2 //必须, 需要唯一
```

重启MySQL.

配置Slave:

```
mysql> change master to master_host='192.168.10.11',master_user='slave01',master_password='123qwe';
mysql> start slave;
```

查看从库状态：

```
mysql>show slave status;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.10.11
                  Master_User: slave01
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: binlog.000006
          Read_Master_Log_Pos: 1155
               Relay_Log_File: mysqld-relay-bin.000009
                Relay_Log_Pos: 1315
        Relay_Master_Log_File: binlog.000006
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
							...
```

之前需要将主库的数据导到从库.


遇到的问题：

在从库执行 `show slave staus` 后发现：

```
Last_IO_Error: Fatal error: The slave I/O thread stops because master and slave have equal MySQL server UUIDs;  these UUIDs must be different for replication to work.
```

在网上找到的原因和解决方案是：

这个错误是由于主从使用了相同的UUID「在主从的server_id确保是各不相同的情况下」.

首先分别在主从机器上查看：

```
[root@master ~]# cat /var/lib/mysql/auto.cnf
[auto]
server-uuid=3636a3ee-21b8-11e6-aebc-080027e27e5e
```

```
[root@slave ~]# cat /var/lib/mysql/auto.cnf
[auto]
server-uuid=3636a3ee-21b8-11e6-aebc-080027e27e5e
```

发现是相同的，可以通过以下方法解决：

在从库机器上：

```
[root@slave ~]# mv /var/lib/mysql/auto.cnf /var/lib/mysql/auto.cnf.back
[root@slave ~]# service mysqld restart
```

通过重启MySQL来重新生成uuid. 最后问题解决.


另外，我们需要在从库上配置授权访问，保证其他服务器可以访问：

```
mysql> grant all on *.* to vagrant@'%' identified by 'vagrant' with grant option;
```

`%`标示任何IP地址都可以进行访问, 当然也可以指定服务器IP, 保证自己的服务器能够访问, 而其他IP不能访问。
