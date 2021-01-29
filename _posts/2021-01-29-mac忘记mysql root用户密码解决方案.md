---
layout: post
title:  "mac忘记mysql root密码解决方案"
date:   2021-01-29 15:14:02 +0800
categories: mysql
---

### 前言
mysql忘记了root密码, 还不能通过其他记住密码的用户更改root的密码, 没这个权限. mysql版本: 8.0.17

### 解决方案: 通过启动mysqld_safe --skip-grant-tables跳过授权解决
- 停止mysql服务: 
```
sudo /usr/local/mysql/support-files/mysql.server stop
``` 

- 到mysqld_safe命令目录下
```
cd /usr/local/mysql/bin
sudo su
```

- 启动跳过授权的mysql
```
./mysqld_safe --skip-grant-tables & 
```

- 打开一个新的终端窗口: command + n

- 登录mysql客户端, 不用输入密码就可以登录成功
```
mysqld
```
```
mysql -uroot -p
```

- 置空mysql.user表的root用户密码为空字符串. 如果不置空, 下一步可能报没有权限的问题.修改了密码之后需要刷新权限, 如果不刷新,下一步执行会报“ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement
”
```
use mysql;
update user set authentication_string='' where user='root'
FLUSH PRIVILEGES;
```

- 更新密码
```
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123password';
FLUSH PRIVILEGES;
```

- 启动mysql 尝试用新密码登录

### 注意事项
- mysql版本不一样, 更新密码的语句也会不同

- 当前解决方案是在mysql服务器端解决通过的

### 思考
- mysqld_safe --skip-grant-tables 是一种什么样的存在
- mysql的账号密码权限体系

### 参考资料
[mac mysql8（版本8） 忘记root密码](https://blog.csdn.net/starkpan/article/details/81057721)   