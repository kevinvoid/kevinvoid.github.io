---
layout: post
title:  "mysql客户端连接失败显示Access denied for user"
date:   2021-01-29 10:34:02 +0800
categories: mysql
---

### 背景
mysql服务器是在192.168.1.71上, 访问mysql服务器的应用是在192.168.1.70, 最近一直没有更新过包, 服务器的变动未知(没有通知到有变动), 应用突然有一天早上启动报错,日志显示连接mysql服务器失败.

### 日志
```
Caused by: com.mysql.cj.exceptions.CJException: Access denied for user 'root'@'192.168.1.70' to database 'ncpcs_clean'
 at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
 at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
 at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
 at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
 at com.mysql.cj.exceptions.ExceptionFactory.createException(ExceptionFactory.java:61)
 at com.mysql.cj.exceptions.ExceptionFactory.createException(ExceptionFactory.java:105)
 at com.mysql.cj.exceptions.ExceptionFactory.createException(ExceptionFactory.java:151)
 at com.mysql.cj.exceptions.ExceptionFactory.createException(ExceptionFactory.java:129)
 at com.mysql.cj.protocol.a.NativeProtocol.checkErrorMessage(NativeProtocol.java:809)
 at com.mysql.cj.protocol.a.NativeProtocol.checkErrorMessage(NativeProtocol.java:734)
 at com.mysql.cj.protocol.a.NativeProtocol.checkErrorMessage(NativeProtocol.java:702)
 at com.mysql.cj.protocol.a.NativeProtocol.checkErrorMessage(NativeProtocol.java:132)
 at com.mysql.cj.protocol.a.NativeAuthenticationProvider.proceedHandshakeWithPluggableAuthentication(NativeAuthenticationProvider.java:540)
 at com.mysql.cj.protocol.a.NativeAuthenticationProvider.connect(NativeAuthenticationProvider.java:202)
 at com.mysql.cj.protocol.a.NativeProtocol.connect(NativeProtocol.java:1442)
 at com.mysql.cj.NativeSession.connect(NativeSession.java:165)
 at com.mysql.cj.jdbc.ConnectionImpl.connectWithRetries(ConnectionImpl.java:849)
 ... 85 common frames omitted
ncpcs-dataflow-normalization-dev 19:23:03.109 [main] INFO  org.hibernate.jpa.internal.util.LogHelp
```

### 分析
- 根据日志“Access denied for user 'root'@'192.168.1.70' to database 'ncpcs_clean'”可以看出从192.168.1.70机器访问mysql服务器(具体服务器的ip日志中一般不会打印出来)的ncpcs_clean库报“Access denied for user”错误
  可以推测出有以下几种可能导致的
  - root用户的密码错误
  - root用户密码正确访问权限不够
  - 当前机器的ip可能被mysql服务器限制访问了

### 排查
- 根据上述的分析, 在报错机器上使用mysql客户端(myslq客户端命令或者可视化界面), 登录显示同样的错误
- 在mysql服务器上可以正常登录, 在一台其他的机器上, 也可以正常登录
- 查看mysql root用户: SELECT * FROM mysql.user WHERE user = 'root' ORDER BY host ASC; 结果显示了“'192.168.1.70', 'root'“操作权限全部是N, 也就是没有权限, 输出如下
```
INSERT INTO `mysql`.`user`(`Host`, `User`, `Select_priv`, `Insert_priv`, `Update_priv`, `Delete_priv`, `Create_priv`, `Drop_priv`, `Reload_priv`, `Shutdown_priv`, `Process_priv`, `File_priv`, `Grant_priv`, `References_priv`, `Index_priv`, `Alter_priv`, `Show_db_priv`, `Super_priv`, `Create_tmp_table_priv`, `Lock_tables_priv`, `Execute_priv`, `Repl_slave_priv`, `Repl_client_priv`, `Create_view_priv`, `Show_view_priv`, `Create_routine_priv`, `Alter_routine_priv`, `Create_user_priv`, `Event_priv`, `Trigger_priv`, `Create_tablespace_priv`, `ssl_type`, `ssl_cipher`, `x509_issuer`, `x509_subject`, `max_questions`, `max_updates`, `max_connections`, `max_user_connections`, `plugin`, `authentication_string`, `password_expired`, `password_last_changed`, `password_lifetime`, `account_locked`) VALUES ('%', 'root', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'N', '', '', '', '', 0, 0, 0, 0, 'mysql_native_password', '*81F5E21E35407D884A6CD4A731AEBFB6AF209E1B', 'N', '2021-01-28 10:36:34', NULL, 'N');
INSERT INTO `mysql`.`user`(`Host`, `User`, `Select_priv`, `Insert_priv`, `Update_priv`, `Delete_priv`, `Create_priv`, `Drop_priv`, `Reload_priv`, `Shutdown_priv`, `Process_priv`, `File_priv`, `Grant_priv`, `References_priv`, `Index_priv`, `Alter_priv`, `Show_db_priv`, `Super_priv`, `Create_tmp_table_priv`, `Lock_tables_priv`, `Execute_priv`, `Repl_slave_priv`, `Repl_client_priv`, `Create_view_priv`, `Show_view_priv`, `Create_routine_priv`, `Alter_routine_priv`, `Create_user_priv`, `Event_priv`, `Trigger_priv`, `Create_tablespace_priv`, `ssl_type`, `ssl_cipher`, `x509_issuer`, `x509_subject`, `max_questions`, `max_updates`, `max_connections`, `max_user_connections`, `plugin`, `authentication_string`, `password_expired`, `password_last_changed`, `password_lifetime`, `account_locked`) VALUES ('192.168.1.70', 'root', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', '', '', '', '', 0, 0, 0, 0, 'mysql_native_password', '*81F5E21E35407D884A6CD4A731AEBFB6AF209E1B', 'N', '2020-12-15 14:30:12', NULL, 'N');
INSERT INTO `mysql`.`user`(`Host`, `User`, `Select_priv`, `Insert_priv`, `Update_priv`, `Delete_priv`, `Create_priv`, `Drop_priv`, `Reload_priv`, `Shutdown_priv`, `Process_priv`, `File_priv`, `Grant_priv`, `References_priv`, `Index_priv`, `Alter_priv`, `Show_db_priv`, `Super_priv`, `Create_tmp_table_priv`, `Lock_tables_priv`, `Execute_priv`, `Repl_slave_priv`, `Repl_client_priv`, `Create_view_priv`, `Show_view_priv`, `Create_routine_priv`, `Alter_routine_priv`, `Create_user_priv`, `Event_priv`, `Trigger_priv`, `Create_tablespace_priv`, `ssl_type`, `ssl_cipher`, `x509_issuer`, `x509_subject`, `max_questions`, `max_updates`, `max_connections`, `max_user_connections`, `plugin`, `authentication_string`, `password_expired`, `password_last_changed`, `password_lifetime`, `account_locked`) VALUES ('localhost', 'root', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', '', '', '', '', 0, 0, 0, 0, 'mysql_native_password', '*81F5E21E35407D884A6CD4A731AEBFB6AF209E1B', 'N', '2021-01-28 10:36:34', NULL, 'N');

```
- 从上述可以判断出从机器ip为192.168.1.70上采用root用户登录操作权限全部是置否的状态, 由于存在“root@%”操作置Y的记录存在, ‘%’表示匹配任意ip, 可以通过删除‘root@192.168.1.70’的记录或者把改记录操作权限全部置成Y解决

### 解决方案
- 删除从mysql.user表中删除‘root@192.168.1.70’记录
- 把‘root@192.168.1.70’这条记录的操作权限置成Y: 实践证明此法操作之后,可以登录成功,但是之后服务器会删除该条记录,具体处理机制未知


### mysql创建新用户并授权的步骤
- Create a new MySQL User Account 
	- 语法: CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'user_password';
	- 说明: use the '%' wildcard as a host part
	- 示例: CREATE USER 'newuser'@'10.8.0.5' IDENTIFIED BY 'user_password';
	- 示例: CREATE USER 'newuser'@'%' IDENTIFIED BY 'user_password';
	- 说明: 创建用户之后, mysql.user表中该用户的所有权限默认都是N,需要执行授权

- Grant Privileges to a MySQL User Account 
	- 语法: GRANT permission1, permission2 ON database_name.table_name TO 'database_user'@'localhost';
	- 示例: GRANT ALL PRIVILEGES ON database_name.* TO 'database_user'@'localhost';
	- 示例: GRANT ALL PRIVILEGES ON *.* TO 'database_user'@'localhost';
	- 示例: GRANT ALL PRIVILEGES ON database_name.table_name TO 'database_user'@'localhost';
	- 示例: GRANT SELECT, INSERT, DELETE ON database_name.* TO database_user@'localhost';

- Display MySQL User Account Privileges 
	- 语法: SHOW GRANTS
	- 示例: SHOW GRANTS FOR 'database_user'@'localhost';


- 创建mercury用户并授权的示例: 
```
 CREATE USER 'mercury'@'%' IDENTIFIED BY 'password';
 GRANT ALL PRIVILEGES ON *.* TO 'mercury'@'%';
```
	- 说明: FLUSH PRIVILEGES; 不需要执行,新建用户正常登录
	- 什么时候flush privileges: https://dev.mysql.com/doc/refman/8.0/en/privilege-changes.html

### 删除mysql用户
- DROP USER 'user'@'localhost'


### 注意事项

### 思考
- mysql服务器在客户端做了什么操作之后会在mysql.user表中生成一条该客户端ip操作权限全部是N的记录,限制其访问操作
- 之前hbase服务器也对hbase客户端做了做了限制该ip访问的权限, 具体的原因未知, 可追溯下mysql和hbase服务器为什么对客户端做此限制
- mysql系统表有哪些及作用
- 什么情景下会导致mysql拒绝某个客户端访问

### 参考资料
[How to Create MySQL Users Accounts and Grant Privileges](https://linuxize.com/post/how-to-create-mysql-user-accounts-and-grant-privileges/)   
[When Privilege Changes Take Effect](https://dev.mysql.com/doc/refman/8.0/en/privilege-changes.html)
[When is Flush Privileges in MySQL really needed?](https://stackoverflow.com/questions/36463966/when-is-flush-privileges-in-mysql-really-needed)

