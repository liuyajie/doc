#### 一、SET指令

```nginx
set $username 'lyj zz';
set $password 123;

# 设置或使用时，中间无空格不需要加引号，中间有空格需要加引号
return 200 $username$password;
return 200 '$username $password';
```

#### 二、IF指令

```nginx
# 1、直接使用变量名
valid_referers none blocked vtest.com;
if ($invalid_referer){
   	return 403;
}

# 2、 = 和 !=  字符串比较
if ($request_method = POST){
   return 200 'please use POST';
}

# 3、正则匹配 ~ 区分大小写 ~* 不区分大小写 ^~取反  ^~*取反
if ($http_user_agent ~* "Mise"){
    return 500 'please use other browser';
}

# 4、-f 判断文件是否存在 -d 判断目录是否存在 -e判断文件或目录是否存在 !-f !-d !-e取反 
if (!-f $request_filename){
    rewrite ^/(.*)$ /index.php/$1;
}
```

#### 三、RETURN指令

```nginx
# 1、用来返回数据
default_type application/json;
return 200 '{"id":1,"uname":"zz"}';

default_type text/html;
return 404 "<h1>404 NOT FOUND</h1>";

# 2、重定向
return 301 https://www.baidu.com;
return 302 https://www.baidu.com;
```

#### 四、BREAK指令

好像没啥用，不用处理！

#### 五、REWRITE指令

```nginx
# 最关键的指令，用来重写URL或重定向URL
# 注意：有四个重要的标识
# break 和 last重写URL
break请求：
    1）请求 linux.rewrite.com/break
    2）匹配 location ~ ^/break 会跳转请求 到 linux.rewrite.com/test
    3）请求跳转后，会去查找本地的站点目录下的 test/index.html;
    4）如果找到了，则返回站点目录下的 test/index.html的内容；
    5）如果没找到该目录则报错404，如果找到该目录没找到对应的文件则403
last请求:
    1）请求 rewrite.drz.com/last
    2）匹配 location ~ ^/last 会跳转请求 到 linux.rewrite.com/test
    2）请求跳转后，会去查找本地的站点目录下的 test/index.html;
    3）如果找到了，则返回站点目录下的 test/index.html的内容；
    4）如果没找到，会对当前server重新的发起一次请求，linux.rewrite.com/test/
    5）如果有location匹配上，则直接返回该location的内容。
    4）如果也没有location匹配，再返回404;
# 结论
last隐含有循环之意，需要谨慎。break没有找见就算了，不会重新请求server。

# redirect 和 permanent 
redirect时
	302 临时重定向
	关闭nginx之后访问失败
permanent时
	301 永久重定向
	关闭nginx仍然访问成功
#结论：
redirect，每次访问服务器都会进行询问，是否进行跳转
permanent，记录一次跳转，以后都不会询问，直接跳转页面，除非清空缓存
```

```nginx
# 1、防盗链
location ~ \.(png|jpeg|jpg|gif) {
     valid_referers none blocked vtest2.com;
     if ($invalid_referer){
         rewrite .* /09.jpg break;
     }
}

# 2、没有写index.php
location / {
    if (!-e $request_filename){
        rewrite ^(.*) /index.php/$1 last;
    }
}

# 3、重定向，一个旧域名跳转到新域名
server {
	rewrite ^(.*) https://www.baidu.com/$1 permanent;
}
# 3、新域名的http跳转到https
server {
    listen 80;
    rewrite ^(.*) https://$server_name$1 redirect;
}


# 4、VUE配置
location / {
    
    if (!-e $request_filename){
        rewrite ^(.*) /index.html$1;
    }
}

```

























