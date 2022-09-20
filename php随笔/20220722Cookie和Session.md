#### 一、Cookie

1. 概念

   由于HTTP协议是无状态的协议，每次请求都与其他次请求无关。你请求服务器，服务器给你响应，这就完成了一次请求。随着互联网发展，需要用户登录，记录用户的操作的情况，这时需要服务器能 *记住* 哪个用户的操作，就让用户每次请求的时候带上一个标识，服务器就能判断是谁的操作了。

2. 用法

   ```php
   // 设置cookie的三种方式
   // 设置cookie，值会自动编码
   // 键 值 有效期 路径 域名 安全设置 httponly
   setcookie('username', '刘亚杰', time() + 10, '/', '', false, true);
   // 与上边一样，这个需要手动编码，不然可能会乱码
setrawcookie('username', rawurlencode('刘亚杰'), time() + 86400 * 365);
   // 使用header函数来设置
   // 设置的内容   
   header("Set-Cookie:username=刘亚杰; expires=" . gmdate('D, d M Y H:i:s \G\M\T', time() + 10)."Max-Age=10; path=/; HttpOnly", false);# 是否替换上边的设置，默认为false
   
   // 删除cookie的两种方式，就是把有效期设置为过期时间，这样浏览器接到之后就会删除它
   setcookie('username','/',time()-1);
   header("Set-Cookie:username=刘亚杰; expires=" . gmdate('D, d M Y H:i:s \G\M\T', time() - 1), false);
   
   // 获取cookie，有一个系统变量存储着接收到的cookie，可以直接使用
   echo $_COOKIE['username'];
   
   // cookie的会话级别
   // 1、不设置过期时间，当前浏览器关闭自动删除
   setcookie('username','刘亚杰');
   // 2、设置过期时间，到时自动删除
   setcookie('username','刘亚杰',time()+10);
   // 3、主动删除
   setcookie('username','刘亚杰',time()-1);
   ```
   
   3.cookie来搞单点登录
   
   ```php
   // a.test.com
   // 子域名可以获取上级域名设置的cookie
   $uid = $_COOKIE['uid'];
   if ($_COOKIE['ticket'] != md5($uid . "ticket")) {
       echo 'a站点授权失败！';
       die;
   }
   ```
   
   ```php
   // b.test.com
   $uid = $_COOKIE['uid'];
   if ($_COOKIE['ticket'] != md5($uid . "ticket")) {
       echo 'b站点授权失败！';
       die;
   }
   ```
   
   ```php
   // 授权中心
   // test.com
   // 登录成功之后，设置一个cookie
   if( false ){
       echo '验证失败';
   }
   setcookie("uid", $uid, time() + 60, "/", "test.com");
   setcookie("ticket", md5($uid . "ticket"), time() + 60, "/", "test.com");
   ```

#### 二、Session

1. 概念

   出现了cookie之后，似乎能满足大部分的功能了，但由于cookie的数据是存储在浏览器中的，数据的安全性无法保证。后来发展出Session，Cookie中存储一个PHPSESSID即可，这是一个标识，拿着这个标识去服务器中查找数据。

2. 用法

   ```php
   // cookie的键，默认是PHPSESSID，自定义之后，每次使用之前也要自定义才行
   session_name('laravel-session-one');
   // 获取设置的session_name
   // echo session_name(); // laravel-session-one
   
   // cookie的值，默认是根据配置项设置的26位字符串
   // 也可以用来自定义的，在客户端禁用cookie之后需要手动指定session_id，因为要来找文件的
   // 会以这个值为名(sess_maj9ld4d7ctm0ttrgm0op7m65b)，新建一个文件来保存session数据
   session_id(); // maj9ld4d7ctm0ttrgm0op7m65b
   // 会以这个值为名(sess_123456)，新建一个文件来保存session数据
   // session_id(123456); // 123456
   
   // 这个函数的作用就是开启session
   // 根据session_name查看cookie中是否存在，如果存在获取session_id。如果没有就会随机生成一个唯一的26位的session_id
   // 通过这个session_id就可以绑定一个唯一的用户。
   // 这个过程还会初始化$_SESSION这个变量，读取session文件中的内容（如果有的话，没有则创建）
   // 把内容反序列化之后赋值到$_SESSION这个变量中，这个阶段还有一个特别关键的作用，
   // 还会判断那些session文件已经过期，调用gc进程，删除掉过期的session文件。
   session_start();
   $_SESSION['uname'] = 'liuyajie';
   ```
   
   ```php
   // 1、开始使用
   // 如果之前设置了session_name，那么本次还得使用session_name设置
   // 如果没有，就正常开启就行了
   session_start();
   $_SESSION['uname'];
   
   // 清理某个变量
   unset($_SESSION['uname']);
   // 清空整个$_SESSION，文件还在的哈
   session_unset();
   
   // 脚本结束之前，仍然可以使用$_SESSION中的内容，因为已经加载到内存中去了
   // 脚本结束之后，删除session_name对应的文件
   session_destroy();
   ```
   
   ```php
   // 2、记住用户
   // 设置session对应的cookie的参数
   // 第一个参数就是cookie的过期时间，默认为0也就是不设置，浏览器关闭cookie自动删除
   // 如果用户点击记住我，那么设置这个时间，浏览器就会保存该cookie了
   session_set_cookie_params(86400*14);
   // 后台也要session.gc_maxlifetime = 86400*14 才行
   ```
   
   ```php
   // 3、用户退出登录时，我们要做的操作
   // 告诉浏览器删除cookie
   setcookie(session_name(),'',time()-1);
   // 删除后台的session文件
   session_start();
   session_destroy();
   ```
   
   ```php
   // 4、客户端禁用session
   // 开启以下配置项，PHP对自己输出的html文件或标签后边加上 ?PHPSESSID=XXXXXXXXXXXXXXX
   ini_set("session.use_trans_sid",1);
   ini_set("session.use_only_cookies",0);
   ini_set("session.use_cookies",1);
   
   echo '<a href='/a'>点我</a>';
   
   // 自己使用的时候，在开启session之前，设置要的PHPSESSID
   if(isset($_GET['PHPSESSID'])){
       session_id($_GET['PHPSESSID']);
   }
   session_start();
   ```
   
   ```ini
    # session_name 以及自动生成 session_id 的随机字符串长度
    session.name = PHPSESSID
    session.sid_length = 26
    # 是否自动开启session
    session.auto_start = 0
    
    # 配置项，session驱动为file时，文件的保存位置
    session.save_handler=files
    session.save_path="C:\phpstudy_pro\Extensions\tmp\tmp"
    # 配置项，session驱动为redis时，服务器的位置
    # 需要开启redis扩展才可以的
    session.save_handler=redis
    session.save_path="tcp://127.0.0.1:6379"
   
   # 浏览器禁用cookie时，可以通过url传值的方式来使用session（自动传值的）
   session.use_trans_sid=1
   session.use_only_cookies=0
   session.use_cookies=1
   
   # cookie的默认过期时间，当前浏览器关闭，自动删除cookie
   session.cookie_lifetime = 0
   
   # session的过期时间
   # 如果驱动为file时，距离文件最后修改时间超过该值时，会清理掉这个文件
   # 如果驱动为memcache或redis时，这个过期时间由缓存软件进行控制
   session.gc_maxlifetime = 1440
   # 如果用户一直在活动，那么这个最后修改时间或过期时间会一直刷新
   # 如果驱动为file时，执行了session_start函数，那么会修改文件的最后修改时间
   # 如果驱动为memcache或redis时，执行了session_start函数，也会修改缓存中的过期时间
   # 如果驱动为file时，代表每次执行session_start时，有1/1000的概率删掉过期的文件
   # 如果驱动为memcache或redis时，过期之后，缓存软件会自动删掉，不用session_start操心
   session.gc_probability = 1
   session.gc_divisor = 1000
   ```

3. 如何准确的设置一个30分钟就过期的session

   ```ini
   # 解决方案1，把session的底层存储改为缓存，然后设置过期时间为30分钟，其实不可行，因为只要执行session_start，就会更新缓存的过期时间
   
   # 解决方案2，把cookie的过期时间和session的过期时间都设置为30分钟，其实也不可行，因为我可以把sessionid放到postman上，继续请求，那么到30分钟之后，仍然不会过期
   
   # 最佳解决方法，把cookie的过期时间和session的过期时间都设置会30分钟，然后把每个session值加上一个时间戳，手动去判断是否过期
   ```

   









