#### 一、Cookie

1、配置

```php
return [
    // cookie 名称前缀
    'prefix'    => '',
    // cookie 保存时间
    'expire'    => 0,
    // cookie 保存路径
    'path'      => '/',
    // cookie 有效域名
    'domain'    => '',
    //  cookie 启用安全传输
    'secure'    => false,
    // httponly设置
    'httponly'  => '',
    // 是否使用 setcookie
    'setcookie' => true,
];
```

2、使用

```php
// 增和改 它二者合起来叫设置
Cookie::set('username','liuyajie',60); // 键 值 有效期
Cookie::set('username','liuyajie',['prefix'=>'test','expire'=>60]);// 键 值 【前缀，有效期】
Cookie::set('udata',['username'=>'liuyajie'],60);// 键 数组 有效期

Cookie::forever('username','liuyajie'); // 键 值 有效期为0，也就是永久保存

// 删
Cookie::delete('username','test');
Cookie::delete('udata.username','test');

Cookie::clear();// 清空所有，当前前缀下的
Cookie::clear('test');// 清空所有，指定前缀下的

// 查
Cookie::get('username','test'); // 键 值
Cookie::has('username','test');//  键 值 ， 返回boolean值，判断有没有设置
```

#### 二、Session

1、配置

```php
return [
    // 调用session_id方法传入的值，空 使用cookie中的PHPSESSID的值，
    // input('param.sessionid') 使用url中传递的值
    'id'             => '',
    // SESSION_ID的提交变量,解决flash上传跨域，定义成sessionid的话
    // input('param.sessionid') 使用url中传递的值
    'var_session_id' => '',
    // SESSION 前缀，也就是所谓的作用域，就是该键下的数组而已
    'prefix'         => 'think',
    
    // 驱动方式 支持redis memcache memcached，默认是files驱动，如果设置别的驱动，可能某些配置项失效
    'type'           => '',
    // redis主机
    'host'       => '127.0.0.1',
     // redis端口
    'port'       => 6379,
     // 密码
    'password'   => '',
    
    // 是否自动开启 SESSION
    'auto_start'     => true,
];
```

2、使用

```php
// 增
Session::set('username','liuyajie','test'); // 键 值 前缀

Session::set('udata',['username'=>'liuyajie'],'test');// 键 二维数组 前缀
Session::set('udata.username','liuyajie','test');// 与上边等价

// 改，与增一模一样
Session::set('username','liuyajie','test'); // 键 值 前缀

// 删
Session::delete('username','test');// 键 前缀
Session::delete('udata.username','test');//二维数组 前缀 

Session::clear();// 清空所有，当前前缀下的
Session::clear('test');// 清空所有，指定前缀下的

// 查
Session::get('username','test'); // 键 前缀
Session::pull('username'); // 键 前缀 ，也就是说获取之后，直接删除
Session::has('username','test'); // 键 前缀 , 返回boolen值，有没有设置这个键

// 增  一次性的增
Session::flash('username','liuyajie','dd');

// 改 一次性的改
Session::flash('username','liuyajie','dd');

// 删 一次性的删
Session::flush();

// 查 只能查询一次，查完就删除，跟pull有些相似
Session::get('username');
```

