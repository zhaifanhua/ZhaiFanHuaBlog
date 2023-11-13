---
title: MySQL开启远程连接
date: 2021-06-11 18:14:05
updated: 2021-06-12 00:15:05
description: MySQL开启远程连接
keywords: MySQL
tags:
  - MySQL
categories:
  - MySQL
---



# MySQL开启远程连接

在Linux上面装完MySQL 或 MariaDB，远程登录报错 Host is not allowed to connect to this MySQL server,是因为没有开启远程登陆。解决方法如下：

1、在装有MySQL的机器上登录MySQL；

```
mysql -u root -p你的密码
```

2、执行下面命令；

```
use mysql;
```

3、执行下面命令，执行完可能会报错，不用管它。

```
update user set host = '%' where user = 'root';
```

4、继续执行命令，来刷新MySQL的权限相关表；

```
FLUSH PRIVILEGES;
```


经过上面4步，就可以解决这个问题了。
