一、多实例启动与关闭相关

```shell
#mysql启动有好几种方法
# 1 直接使用二进制文件，也就是exe启动
mysqld --defaults-file=/etc/my.cnf & # mysqld是二进制文件，用它来启动

# 2 使用脚本启动，mysqld_safe是一个脚本，也是个守护进程，当发现mysqld关闭时，自动重启它
mysqld_safe & 

# 3 也是使用脚本启动，也同样有守护进程，其实用的就是第二种
cp mysql.server /etc/init.d/mysql.server start


#关闭有三种
# 1 进入到mysql内部 需要账号密码
shutdown;

# 2 使用mysqladmin关闭 需要账号密码
mysqladmin -uroot -p1111aaA_ -S/tmp/mysql.sock shutdown

# 3 使用 kill关闭 这也是一种平滑关闭
kill 6593 # 平滑关闭，收到信号之后平滑关闭，守护进程不会重新拉起来

kill -9 6593 #强制关闭，这样会出发守护进程重新拉起来mysqld
```



```shell
[client]
user=root # 账号密码不要放错地方，不然多实例启动关闭不了
password=1111aaA_ # 账号密码不要放错地方，不然多实例启动关闭不了

[mysql]
prompt=(\\u@\\h) [\\d]>\\_

[mysqld]
port=3306
user=mysql
datadir=/mdata/mysql_test_data
log_error=error.log
#skip-grant-tables

# 配置文件千万不要弄错，否则不生效
[mysqld_multi]
mysqld=/usr/local/mysql/bin/mysqld_safe # 启动脚本，用来启动mysqld程序
mysqladmin=/usr/local/mysql/bin/mysqladmin # 用来关闭
log=/usr/local/mysql/mysqld_multi.log
# user=root 
# pass=1111aaA_ # 一定要注意，这个地方是pass，否则不生效

[mysqld1]
port=3307
datadir=/mdata/mysql_test_data1
socket=/tmp/mysql.sock1

[mysqld2]
port=3308
datadir=/mdata/mysql_test_data2
socket=/tmp/mysql.sock2
```

二、忘记密码

```shell
[mysqld]
skip-grant-tables # 配置文件中添加这个跳过密码验证	

#重启mysql，无密码进入，修改当前用户的密码
set password='';#无法使用简洁方式，只能使用复杂方式修改密码
update user set authentication_string=password('123456') where user='root';
flush privileges;

#去除该配置，然后重启，这样密码就有了
```

