#### 一、Cookie

1. 概念

   由于HTTP协议是无状态的协议，每次请求都与其他次请求无关。你请求服务器，服务器给你响应，这就完成了一次请求。随着互联网发展，需要用户登录，记录用户的操作的情况，这时需要服务器能 *记住* 哪个用户的操作，就让用户每次请求的时候带上一个标识，服务器就能判断是谁的操作了。

2. 用法

   ```php
   <?php
       // 响应给浏览器，下次浏览器请求的时候会带上该标识
       // Cookie: username=zz
       setcookie('username','zz',time()+5);
   ```

   ```php
   <?php
   	var_dump($_COOKIE);
   ```

#### 二、Session

1. 概念

   出现了cookie之后，似乎能满足大部分的功能了，但由于cookie的数据时存储在浏览器中的，数据的安全性无法保证。后来发展出Session，Cookie中存储一个PHPSESSID即可，这是一个标识，拿着这个标识去服务器中查找数据。

2. 用法

   ```php
   // 使用session之前，需要开启session
   // 这个时候cookie传这个值即可
   // Cookie: PHPSESSID=b654btjd3i701oqpfasdl5j7sp
   session_start();
   $_SESSION['username'] = 'zz';
   ```
   
```ini
   # 配置项，session驱动为file时，文件的保存位置
   session.save_handler=files
   session.save_path="C:\phpstudy_pro\Extensions\tmp\tmp"
   # 配置项，session驱动为redis时，服务器的位置
   session.save_handler=redis
   session.save_path="tcp://127.0.0.1:6379"
   
   # 浏览器禁用cookie时，可以通过url传值的方式来使用session
   session.use_trans_sid=1
   session.use_only_cookies=0
   session.use_cookies=1
   # cookie的默认过期时间，会告诉浏览器，过期之后，不要给我发PHPSESSID，这样也算是简陋版的过期
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
   
   ```php
   // 禁用cookie时，url把sessionid传过来，设置使用该sessionid的数据即可
   // tp5.com/index/index/index/PHPSESSID/b654btjd3i701oqpfasdl5j7sp
   session_id($_GET['PHPSESSID']);
   session_start();
   ```
   
3. 如何准确的设置一个30分钟就过期的session

   ```ini
   # 解决方案1，把session的底层存储改为缓存，然后设置过期时间为30分钟，其实不可行，因为只要执行session_start，就会更新缓存的过期时间
   
   # 解决方案2，把cookie的过期时间和session的过期时间都设置会30分钟，其实也不可行，因为我可以把sessionid放到postman上，继续请求，那么到30分钟之后，仍然不会过期
   
   # 最佳解决方法，把cookie的过期时间和session的过期时间都设置会30分钟，然后把每个session值加上一个时间戳，手动去判断是否过期
   ```

   









