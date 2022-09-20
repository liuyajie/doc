#### 一、下载安装

```shell

$> groupadd mysql
$> useradd -r -g mysql -s /bin/false mysql
$> cd /usr/local
$> tar zxvf /path/to/mysql-VERSION-OS.tar.gz
$> ln -s full-path-to-mysql-VERSION-OS mysql
$> cd mysql
$> scripts/mysql_install_db --user=mysql
$> bin/mysql_ssl_rsa_setup # 安装ssl连接需要的秘钥及开启ssl连接
$> bin/mysqld_safe --user=mysql & # 安全启动，或者用下边的启动也可以
# Next command is optional
$> cp support-files/mysql.server /etc/init.d/mysql.server

# 开机自动启动
$> chkconfig --add mysql.server
$> chkconfig --list mysql.server
```

#### 二、配置文件

```shell
[client]
user=root
password=123456

[mysql]
prompt=(\\u@\\h) [\\d]>\\_

[mysqld]
port=3306
user=mysql
datadir=/mdata/mysql_test_data
log_error=error.log
plugin-load-add=validate_password.so#安装密码插件
```

#### 三、mysql相关

```shell
mysqld --help --verbose #查看mysql的默认配置

mysql --help | grep my.cnf # 查看配置文件的使用顺序

mysql_upgrade -s # 切换软连，原地升级
```























