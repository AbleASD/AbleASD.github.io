``` shell
sudo apt install mysql-server
sudo apt install mysql-client


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


