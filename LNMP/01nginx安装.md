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

```
mkdir /data/www
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



















