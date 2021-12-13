#### 1、卸载已经存在的mysql

```shell
rpm -qa | grep mysql
rpm -e mysql
rpm -qa | grep mariadb
rpm -e mariadb

# 如果有相关依赖
yum remove -y mariadb
```

#### 2、下载mysql源码

```shell
# https://downloads.mysql.com/archives/community/ 官网
# 下载带boost的版本安装
wget https://downloads.mysql.com/archives/get/p/23/file/mysql-boost-8.0.26.tar.gz
tar -zxvf mysql-boost-8.0.26.tar.gz
```

#### 3、安装需要的依赖

```
yum install -y cmake make gcc gcc-c++ wget ncurses-devel perl ncurses-devel openssl-devel bison-devel libaio libaio-devel
```

#### 4、添加用户和用户组

```
groupadd mysql
useradd -r -s /bin/false -g mysql mysql
```

#### 5、编译安装

```shell
cd /usr/local/src/mysql-8.0.26/
mkdir build
cd build

# gcc版本符合要求 低版本需要cmake 高版本需要cmake3
cmake3 \
-DCMAKE_BUILD_TYPE=RelWithDebInfo \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \
-DDEFAULT_CHARSET=utf8mb4 \
-DDEFAULT_COLLATION=utf8mb4_general_ci \
-DMYSQL_DATADIR=/usr/local/mysql/data \
-DSYSCONFDIR=/etc/my.cnf \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DENABLED_LOCAL_INFILE=1 \
-DENABLED_PROFILING=1 \
-DMYSQL_TCP_PORT=3306 \
-DWITH_DEBUG=0 \
-DWITH_SSL=yes \
-DDOWNLOAD_BOOST=1 \
-DWITH_BOOST=../boost/boost_1_73_0/  ..

# gcc版本太低时 需要安装devtoolset工具，指定相应的版本
cmake \
-DCMAKE_BUILD_TYPE=RelWithDebInfo \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \
-DDEFAULT_CHARSET=utf8mb4 \
-DDEFAULT_COLLATION=utf8mb4_general_ci \
-DMYSQL_DATADIR=/usr/local/mysql/data \
-DSYSCONFDIR=/etc/my.cnf \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DENABLED_LOCAL_INFILE=1 \
-DENABLED_PROFILING=1 \
-DMYSQL_TCP_PORT=3306 \
-DWITH_DEBUG=0 \
-DWITH_SSL=yes \
-DDOWNLOAD_BOOST=1 \
-DWITH_BOOST=../boost/boost_1_73_0/ \
-DCMAKE_C_COMPILER=/opt/rh/devtoolset-10/root/usr/bin/gcc \
-DCMAKE_CXX_COMPILER=/opt/rh/devtoolset-10/root/usr/bin/g++ ..

make && make install
```

#### 6、遇见到的坑

```shell
# cmake版本太低，需要安装更高版本的cmake

# 第一种，yum 安装cmake3
yum install -y cmake3
# 包有可能不存在，更新扩展包的源
yum install -y epel-release
# 另一种方式安装epel-release
wget https://download-ib01.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-14.noarch.rpm
rpm -ivh epel-release-7-14.noarch.rpm

# 第二种，源码 安装cmake3
# https://github.com/Kitware/CMake
wget https://github.com/Kitware/CMake/releases/download/v3.22.0/cmake-3.22.0.tar.gz
tar -zxvf cmake-3.22.0.tar.gz
cd cmake-3.22.0
./bootstrap
make && make install

# 做一个软连接
ln -s /usr/bin/cmake3 /usr/bin/cmake

# 如果使用其他gmake可能会安装到/usr/local/bin里头
```

```shell
CMake Warning at CMakeLists.txt:178 (MESSAGE):
  Could not find devtoolset gcc
  
# 缺少devtoolset 或 gcc版本过低
# devtoolset工具带高版本的gcc
yum install -y centos-release-scl-rh centos-release-scl
yum check-update
yum install -y devtoolset-10-gcc  devtoolset-10-gcc-c++

# 可以更改，也可以不更改，都能安装mysql
source /opt/rh/devtoolset-10/enable

gcc -v
```

#### 7、初始化

```shell
cd /usr/local/mysql

./bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data  --initialize
```

```shell
vim /etc/my.cnf

[mysqld]
port=3306
datadir=/usr/local/mysql/data
socket=/usr/local/mysql/mysql.sock
user=mysql
max_connections=20
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
 
# 设置忽略大小写
lower_case_table_names=1
 
# 指定编码
character-set-server=utf8mb4
collation-server=utf8mb4_general_ci
 
# 开启ip绑定
bind-address=0.0.0.0
 
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/usr/local/mysql/mysql.pid
 
#指定客户端连接mysql时的socket通信文件路径
[client]
socket=/usr/local/mysql/mysql.sock
default-character-set=utf8mb4
```

#### 8、运维

```shell
#设置环境变量
vim /etc/profile
PATH=$PATH:/usr/local/mysql/bin
export PATH

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
```



































