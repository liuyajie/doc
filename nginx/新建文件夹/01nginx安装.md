#### 一、源码安装

##### 1、下载源码

```shell
# https://nginx.org/en/download.html nginx的下载地址
cd /usr/local/src
wget https://nginx.org/download/nginx-1.20.2.tar.gz
```

##### 2、安装所需的依赖

```shell
# gcc gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel
rpm -qa | grep pcre # 查看是否安装
rpm -qa | grep pcre-devel

yum install -y pcre
yum install -y pcre-devel
```

##### 3、安装nginx

```shell
tar -zxvf nginx-1.20.2.tar.gz
cd nginx-1.20.2
./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_stub_status_module --with-pcre

make && make install
```

```
# 安装的信息
Configuration summary
  + using system PCRE library
  + using system OpenSSL library
  + using system zlib library

  nginx path prefix: "/usr/local/nginx" # 安装的目录
  nginx binary file: "/usr/local/nginx/sbin/nginx"  # 启动文件
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf" # 配置文件
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"  # 错误日志
  nginx http access log file: "/usr/local/nginx/logs/access.log" # 访问日志
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp
```

##### 4、运维nginx

```shell
./sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf # 测试nginx的配置文件是否可用

vim /lib/systemd/system/nginx.service

# 将以下内容复制进去
[Unit]
Description=nginx.server
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target

systemctl daemon-reload # 加载配置

systemctl start nginx # 启动
systemctl stop nginx # 停止
systemctl status nginx # 查看状态
systemctl reload nginx # 平滑加载配置
systemctl restart nginx # 重启

systemctl enable nginx # 开机自启动
systemctl disable nginx # 禁止开启自启动
```

##### 5、nginx的相关配置

```shell
mkdir /data/www
# 添加用户
groupadd www
useradd -r -s /bin/false -g www www
chown www:www /data/www
```

```shell
user  www www;
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;
	    root "/data/www/";
	    
	    location / {
            index index.php index.html index.htm;
	        autoindex on;
	        autoindex_exact_size on;
	        autoindex_localtime on;		
        }
        
        location ~ \.php(.*)$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_split_path_info  ^((?U).+\.php)(/?.+)$;
	        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            fastcgi_param  PATH_INFO  $fastcgi_path_info;
            fastcgi_param  PATH_TRANSLATED  $document_root$fastcgi_path_info;
            include    fastcgi_params;
            include    fastcgi.conf;
        }
    }
    
}
```

#### 二、yum安装

```shell
# 网络工具包
yum install -y net-tools
netstat -lntup # 查看监听的端口
# 安装常用的工具
yum install -y wget httpd-tools vim tree
tree / # 查看目录结构

# 安装所需的依赖
yum install -y gcc gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel

# 查看这个文档，即可安装
# http://nginx.org/en/linux_packages.html#RHEL-CentOS

# 关闭防火墙
systemctl status firewalld
systemctl stop firewalld

# 关闭安全组件
getenforce # 查看安全组件的状态
setenforce 0 # 关闭安全组件
```

```shell
# 创建一些常用的目录
mkdir /soft/{code,logs,package/src} -p

# 查看nginx的版本
nginx -v
# 查看nginx编译时的配置参数
nginx -V 

# 查看nginx安装了啥都在哪些目录
rpm -ql nginx
rpm -qc nginx # 查看配置目录在哪
```



```
server {
    # 配置DNS解析IP地址，比如 Google Public DNS，以及超时时间（5秒）
    resolver 8.8.8.8;    # 必需
    resolver_timeout 5s;

    # 监听端口
    listen 8080;

    access_log  /home/reistlin/logs/proxy.access.log;
    error_log   /home/reistlin/logs/proxy.error.log;

    location / {
        # 配置正向代理参数
        proxy_pass $scheme://$host$request_uri;
        # 解决如果URL中带"."后Nginx 503错误
        proxy_set_header Host $http_host;

        # 配置缓存大小
        proxy_buffers 256 4k;
        # 关闭磁盘缓存读写减少I/O
        proxy_max_temp_file_size 0;
         # 代理连接超时时间
        proxy_connect_timeout 30;

        # 配置代理服务器HTTP状态缓存时间
        proxy_cache_valid 200 302 10m;
        proxy_cache_valid 301 1h;
        proxy_cache_valid any 1m;
    }
}


proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;






#! /bin/bash

# 配置文件
CONFIG_FILE="/etc/tinyproxy/tinyproxy.conf"

# 下载
wget -P /opt/software https://github.com/tinyproxy/tinyproxy/releases/download/1.11.0-rc1/tinyproxy-1.11.0-rc1.tar.gz

# 解压
mkdir /opt/module
cd /opt/software
tar -zxvf tinyproxy-1.11.0-rc1.tar.gz -C /opt/module/

# 编译安装
cd /opt/module/tinyproxy-1.11.0-rc1/
yum -y install gcc

./configure
make
make install

# 添加配置
mkdir /etc/tinyproxy
#cp /usr/local/etc/tinyproxy/tinyproxy.conf /etc/tinyproxy/tinyproxy.conf
echo "User nobody" >> $CONFIG_FILE
echo "Group nobody" >> $CONFIG_FILE
echo "Port 8888" >> $CONFIG_FILE
echo "BindSame yes" >> $CONFIG_FILE
echo "Timeout 20" >> $CONFIG_FILE
echo "DefaultErrorFile \"/usr/local/share/tinyproxy/default.html\"" >> $CONFIG_FILE
echo "StatFile \"/usr/local/share/tinyproxy/stats.html\"" >> $CONFIG_FILE
echo "LogFile \"/var/log/tinyproxy/tinyproxy.log\"" >> $CONFIG_FILE
echo "LogLevel Info" >> $CONFIG_FILE
echo "MaxClients 500" >> $CONFIG_FILE
echo "ViaProxyName \"tinyproxy\"" >> $CONFIG_FILE
echo "BasicAuth admin 123456" >> $CONFIG_FILE

# 创建日志
mkdir /var/log/tinyproxy
touch /var/log/tinyproxy/tinyproxy.log
chmod 777 /var/log/tinyproxy/tinyproxy.log

echo '启动'
ps -ef|grep tinyproxy|grep -v grep|awk '{print "kill -9 "$2}'|sh
nohup tinyproxy -d -c /etc/tinyproxy/tinyproxy.conf > /dev/null 2>&1 &
```

















