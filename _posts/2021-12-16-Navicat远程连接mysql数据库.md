---
layout: post
title: Navicat 远程连接数据库
categories: [sql, 环境配置]
description: Navicat 远程连接数据库
keywords: sql, 环境配置
---
mysql客户端和服务端

``` shell
sudo apt install mysql-server
sudo apt install mysql-client

设置密码
``` mysql
update user 
set 
    password=password(‘123’) 
where 
    user=’root’;
flush privileges;
```

``` shell
sudo netstat -an | grep 3306

# we get
# tcp   0   0   127.0.0.1:3306  0.0.0.0:*   LISTEN

vim /etc/mysql/mysql.conf.d/mysqld.cnf

# comment this line
# bind-address          = 127.0.0.1
```

``` mysql
use mysql;

update user 
set 
    host = '%' 
where
    user = 'root';
    
flush privileges;
```


