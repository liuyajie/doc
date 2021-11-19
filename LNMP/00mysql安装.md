### 一、yum安装

#### 一、卸载已经存在的版本

```shell
rpm -qa | grep mysql #查看是否已经安装mysql
rpm -e --nodeps mysql-server #卸载mysql

rpm -qa | grep mariadb #查看是否已经安装mariadb
rpm -e --nodeps mariadb-server #卸载mariadb
```

#### 二、添加mysql的yum源

```shell
cat /etc/redhat-release #查看系统版本
# https://dev.mysql.com/downloads/repo/yum/ 官网给的源地址包，需要安装才能有

wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm #下载源的rpm包，然后安装
rpm -Uvh mysql80-community-release-el7-3.noarch.rpm 

ls /etc/yum.repos.d/ # mysql-community.repo mysql-community-source.repo 如果存在则安装成功
yum repolist enabled | grep "mysql*.community*" # 查看相关资源

# 可以选择不同的mysql版本
yum repolist all | grep mysql # 查看有多少个版本可以选择

yum-config-manager --disable mysql80-community # 禁用8.0版本
yum-config-manager --enable mysql57-community # 启用5.7版本，如果有多个版本，则默认最新的

vim /etc/yum.repos.d/mysql-community.repo # 编辑文件也可以禁用和启用某个版本
# enabled = 1 启用
# enabled = 0 禁用
```

#### 三、安装

```shell
yum install -y mysql-community-server #该命令会安装MySQL服务器 (mysql-community-server) 及其所需的依赖、相关组件，包括mysql-community-client、mysql-community-common、mysql-community-libs等
```

#### 四、启动

```shell
systemctl start mysqld #启动
systemctl stop mysqld #停止
systemctl status mysqld #查看状态
systemctl restart mysqld #重启

systemctl enable mysqld #开启自启动
systemctl disable mysqld #取消开机自启动

systemctl daemon-reload #重新加载配置
```

#### 五、mysql初始密码

```shell
cat /var/log/mysqld.log | grep 'temporary password' #获取初始密码

mysql -uroot -p'mkjljfk)233' -hlocalhost

alter user 'root'@'localhost' identified by '123456'; #修改密码，如果有密码安全策略，需要修改成复杂一点的密码，或者修改取消掉密码策略，vim /etc/my.cnf validate_password=off

user mysql;
update user set host='%' where user='root'; #设置远程访问
flush privileges;

grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;#设置远程访问，比前者多添加一个账号
flush privileges;
```

### 二、 rpm安装 （yum的离线版安装）

#### 一、卸载

略

#### 二、下载rpm包

```shell
# https://dev.mysql.com/downloads/mysql/ rpm包地址，选好版本，不要弄错了
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.27-1.el7.x86_64.rpm-bundle.tar
```

#### 三、安装

```shell
tar -xvf mysql-8.0.27-1.el7.x86_64.rpm-bundle.tar # 解压

# 主要是安装这四个文件
rpm -ivh mysql-community-libs-8.0.27-1.el7.x86_64.rpm
rpm -ivh mysql-community-common-8.0.27-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-8.0.27-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-8.0.27-1.el7.x86_64.rpm

# 或使用yum这种方式安装，注意一定要在同一个目录中才可以使用
yum install -y mysql-community-{server,client,common,libs}-*
```

### 三、tar.gz安装（编译好的版本）

#### 一、卸载

略

#### 二、下载

```shell
# https://dev.mysql.com/downloads/mysql/ 不要选错了，.tar.gz结尾的文件

wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.27-el7-x86_64.tar.gz
```

#### 三、安装

```shell
tar -zxvf mysql-8.0.27-el7-x86_64.tar.gz -C /usr/local # 解压

mv /usr/local/mysql-8.0.27-el7-x86_64 /usr/local/mysql # 改名

group add mysql #添加用户以及修改目录主人为mysql
useradd -r -s /bin/false -g mysql mysql
chown -R mysql:mysql /usr/local/mysql

cd /usr/local/mysql

# 安装及初始化一些参数
./bin/mysqld --defaults-file=/etc/my.cnf --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --lower_case_table_names=1 --initialize

# 如果出错了没有关系，删除/usr/local/mysql/data目录即可
```

```
vim /etc/my.cnf

[mysqld]
port=3306
datadir=/usr/local/mysql/data
socket=/usr/local/mysql/mysql.sock
user=mysql
max_connections=151
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
 
# 设置忽略大小写
lower_case_table_names = 1
 
# 指定编码
character-set-server=utf8
collation-server=utf8_general_ci
 
# 开启ip绑定
bind-address = 0.0.0.0
 
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
 
#指定客户端连接mysql时的socket通信文件路径
[client]
socket=/usr/local/mysql/mysql.sock
 
default-character-set=utf8
```

#### 四、启动mysql

```shell
# 第一种方式，非常不推荐也不友好
./support-files/mysql.server start

# 第二种方式，也能用，还不太友好
cp ./support-files/mysql.server /etc/init.d/mysqld

service mysqld start|stop|restart|status #启动相关

chkconfig --add mysqld # 开机自启动
chkconfig --list

# 第三种方式，非常推荐
cp ./support-files/mysql.server /etc/init.d/mysqld

vim /usr/lib/systemd/system/mysqld.service

# 1，添加以下内容到文件中
[Unit]
Description=MySQL Server
After=network.target
After=syslog.target
[Service]
User=mysql
Group=mysql
Type=forking
PermissionsStartOnly=true
ExecStart= /etc/init.d/mysqld start
ExecStop= /etc/init.d/mysqld stop
ExecReload= /etc/init.d/mysqld restart
LimitNOFILE = 5000
[Install]
WantedBy=multi-user.target

# 2，重新加载一下服务的配置文件
systemctl daemon-reload

# 3，就可以像下边这样使用它了
systemctl status mysqld

systemct disable mysqld #禁止开机自启动
```





































































