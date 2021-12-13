#### 一、源码安装

##### 1、下载

```shell
# https://www.php.net/downloads.php 官网的下载地址
cd /usr/local/src
wget https://www.php.net/distributions/php-7.4.26.tar.gz
# 添加一个用户，启动php-fpm时需要使用
groupadd www
useradd -r -s /bin/false -g www www
```

##### 2、安装所需的依赖

````shell
yum install -y libxml2 libxml2-devel openssl openssl-devel bzip2 bzip2-devel libcurl libcurl-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel gmp gmp-devel libmcrypt libmcrypt-devel readline readline-devel libxslt libxslt-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel ncurses curl gdbm-devel db4-devel libXpm-devel libX11-devel gd-devel gmp-devel expat-devel xmlrpc-c xmlrpc-c-devel libicu-devel libmcrypt-devel libmemcached-devel sqlite sqlite-devel
````

##### 3、安装

```shell
# 添加用户
groupadd www
useradd -r -s /bin/false -g www www

tar -zxvf php-7.4.26.tar.gz
cd php-7.4.26
# 安装
./configure \
--prefix=/usr/local/php \
--with-config-file-path=/etc \
--with-fpm-user=www \
--with-fpm-group=www \
--with-curl \
--with-freetype \
--with-gettext \
--with-iconv-dir \
--with-kerberos \
--with-libdir=lib64 \
--with-mysqli \
--with-openssl \
--with-pdo-mysql \
--with-pdo-sqlite \
--with-pear \
--with-jpeg \
--with-xmlrpc \
--with-xsl \
--with-zlib \
--with-bz2 \
--with-mhash \
--with-zip \
--with-libxml \
--with-external-pcre  \
--enable-gd \
--enable-bcmath \
--enable-inline-optimization \
--enable-mbregex \
--enable-mbstring \
--enable-opcache \
--enable-pcntl \
--enable-shmop \
--enable-soap \
--enable-sockets \
--enable-sysvsem \
--enable-sysvshm \
--enable-xml \
--enable-fpm

make && make install
```

##### 4、可能遇见的问题

```shell
configure: error: Package requirements (libpcre2-8 >= 10.30) were not met:

No package 'libpcre2-8' found

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables PCRE2_CFLAGS
and PCRE2_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details.


# https://sourceforge.net/projects/pcre/files/ libpcre2 官网
wget --no-check-certificate https://jaist.dl.sourceforge.net/project/pcre/pcre2/10.37/pcre2-10.37.tar.gz
./configure
make && make install

# 配置环境变量
vim /etc/profile # 最后插入以下内容
PKG_CONFIG_PATH=/usr/local/lib/pkgconfig/
export PKG_CONFIG_PATH

source /etc/profile # 执行一下即可，注意用户变量和环境变量的区别
```

```shell
configure: error: Package requirements (sqlite3 > 3.7.4) were not met:

No package 'sqlite3' found

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables SQLITE_CFLAGS
and SQLITE_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details.

# 先查看是否存在
rpm -qa | grep sqlite
yum install -y sqlite-devel # 安装
```

```shell
configure: error: Package requirements (oniguruma) were not met:

No package 'oniguruma' found

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables ONIG_CFLAGS
and ONIG_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details.

# 第一种，yum安装
yum install -y oniguruma oniguruma-devel
# 如果没有这个包的话，需要安装扩展包
yum install -y epel-release
yum install -y oniguruma oniguruma-devel

# 第二种，二进制包安装
# https://pkgs.org/ 官网

wget https://download-ib01.fedoraproject.org/pub/epel/7/x86_64/Packages/o/oniguruma-6.8.2-1.el7.x86_64.rpm
wget https://download-ib01.fedoraproject.org/pub/epel/7/x86_64/Packages/o/oniguruma-devel-6.8.2-1.el7.x86_64.rpm

rpm -ivh oniguruma-6.8.2-1.el7.x86_64.rpm
rpm -ivh oniguruma-devel-6.8.2-1.el7.x86_64.rpm

# 第三种，源码安装
# https://github.com/kkos/oniguruma/releases 官网

wget https://github.com/kkos/oniguruma/archive/refs/tags/v6.9.7.1.zip -O oniguruma-6.9.7.1.tar.gz
tar -zxvf oniguruma-6.9.7.1.tar.gz
cd oniguruma-6.9.7.1
./autogen.sh
./configure
make && make install

# 配置环境变量
vim /etc/profile # 最后插入以下内容 
PKG_CONFIG_PATH=/usr/local/lib/pkgconfig/
export PKG_CONFIG_PATH

source /etc/profile # 执行一下即可，注意用户变量和环境变量的区别
```

```shell
configure: error: Package requirements (libzip >= 0.11 libzip != 1.3.1 libzip != 1.7.0) were not met:

Requested 'libzip >= 0.11' but version of libzip is 0.10.1

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables LIBZIP_CFLAGS
and LIBZIP_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details.

# 第一种，yum安装libzip，有可能软件版本太低不满足要求，如果不满足要求，卸载即可
yum install -y libzip libzip-devel
rpm -e libzip
rpm -e libzip-devel

# 第二种，rpm安装
yum install -y http://mirror.centos.org/centos/8/AppStream/x86_64/os/Packages/libzip-1.5.2-1.module_el8.2.0+314+53b99e08.x86_64.rpm

yum install -y http://mirror.centos.org/centos/8/AppStream/x86_64/os/Packages/libzip-devel-1.5.2-1.module_el8.2.0+314+53b99e08.x86_64.rpm

# 第三种，源码安装libzip，1.2版本已经编译好了，直接使用安装即可，更高版本的需要自己编译。
# 如果报错未找到，看看环境变量PKG_CONFIG_PATH是否设置正确
# https://libzip.org/news/ 官网
wget https://libzip.org/download/libzip-1.2.0.tar.gz
tar -zxvf libzip-1.2.0.tar.gz
cd libzip-1.2.0
./configure
make && make install

# 第四种，源码安装libzip，该包需要cmake3进行编译
# https://libzip.org 官网
wget --no-check-certificate https://libzip.org/download/libzip-1.8.0.tar.gz
tar -zxvf libzip-1.8.0.tar.gz
cd libzip-1.8.0
mkdir build
cd build
cmake3 -DCMAKE_INSTALL_PREFIX=/usr/local/ ..  #指定目录及源码所在地
make 
make install
# libzip包安装在了/usr/lib64下边了，需要指定PKG_CONFIG_PATH


# 第一种，yum 安装cmake3，包有可能不存在，更新扩展包的源
yum install -y epel-release
yum install -y cmake3
# 另一种方式安装epel-release
wget https://download-ib01.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-14.noarch.rpm
rpm -ivh epel-release-7-14.noarch.rpm

# 第二种，源码 安装cmake3
# https://github.com/Kitware/CMake
wget https://github.com/Kitware/CMake/releases/download/v3.22.0/cmake-3.22.0.tar.gz
tar -zxvf cmake-3.22.0.tar.gz
cd cmake-3.22.0
./bootstrape
make && make install
make test # 检查一下看是否安装正常
```

##### 5、运维php

1、配置

```shell
/usr/local/php/bin/php -v  # 测试是否安装成功
/usr/local/php/bin/php -r "phpinfo();"| grep ini

#添加环境变量
vim /etc/profile
PATH=$PATH:/usr/local/php/bin/
export PATH
source /etc/profile

#进入src目录
cp php.ini-production /etc/php.ini
cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf

#编辑
vim /usr/local/php/etc/php-fpm.conf

pid = run/php-fpm.pid
error_log = log/php-fpm.log
log_level = notice
log_limit = 4096

#编辑
vim /lib/systemd/system/php-fpm.service

[Unit]
Description=The PHP FastCGI Process Manager
After=syslog.target network.target

[Service]
Type=simple
PIDFile=/usr/local/php/var/run/php-fpm.pid
ExecStart=/usr/local/php/sbin/php-fpm --nodaemonize --fpm-config /usr/local/php/etc/php-fpm.conf
ExecReload=/bin/kill -USR2 $MAINPID
ExecStop=/bin/kill -SIGINT $MAINPID

[Install]
WantedBy=multi-user.target

systemct daemon-reload
```

2、运维

```shell
systemctl start php-fpm
systemctl status php-fpm


systemctl enable php-fpm
```

3、安装扩展

```shell
# http://pecl.php.net/ 扩展官网

wget http://pecl.php.net/get/redis-5.3.4.tgz
tar -zxvf redis-5.3.4.tgz
cd redis-5.3.4
phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install

vim /etc/php.ini
extension=redis.so

systemctl reload php-fpm
```





































