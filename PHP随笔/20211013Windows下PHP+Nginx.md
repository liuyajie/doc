1、在 Windows 下，PHP没有php-fpm这个软件，有php-cgi.exe，它是用来管理请求的。

```shell
php-cgi.exe -b 127.0.0.1:9000 -c php.ini #监听9000端口，且使用php.ini这个配置
```

2、在 PHPstudy这个软件中，开启Nginx时会自动开启php-cgi.exe，接收请求，处理请求，返回结果。

3、如果使用Apache作为服务器，则不需要开启php-cgi.exe或php-fpm，Apache使用的是mod-php模块，然后调用php.exe处理关于php脚本的。