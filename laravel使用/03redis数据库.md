```php
# 安装predis包
composer require predis/predis
# 安装redis扩展
# 自己去下载安装
```

```php
// 配置文件
'redis' => [
    // 如果是predis包，设置成predis
    // 如果是redis扩展，设置成phpredis
    'client' => env('REDIS_CLIENT', 'predis'),
    'options' => [
        'cluster' => env('REDIS_CLUSTER', 'predis'),
        // 前缀
        'prefix'  => env('REDIS_PREFIX', Str::slug(env('APP_NAME', 'laravel'), '_') . '_database_'),
    ],
    'default' => [
        'url'          => env('REDIS_URL'),
        'host'         => env('REDIS_HOST', '127.0.0.1'),
        'password'     => env('REDIS_PASSWORD', null),
        'port'         => env('REDIS_PORT', 6379),
        // 默认选择可能有问题，最好不要设置为0
        'database'     => env('REDIS_DB', 0),
    ],
    'cache' => [
        'url'      => env('REDIS_URL'),
        'host'     => env('REDIS_HOST', '127.0.0.1'),
        'password' => env('REDIS_PASSWORD', null),
        'port'     => env('REDIS_PORT', 6379),
        'database' => env('REDIS_CACHE_DB', 1),
    ],

],
```

```php
// 发布订阅
// php artisan make:command redis:OrderSubscribe

public function handle()
{
    // $redis = app('redis.connection');
    // 获取连接实例
    $redis = Redis::connection();
    // 第一种方式设置不过期时间在此设置，配置文件中设置有点问题
    $redis->setOption(\Redis::OPT_READ_TIMEOUT, -1);
    // 第二种方式设置不过期时间
    // ini_set('default_socket_timeout', -1);
    
    // 只能设置一个，谁先来谁阻塞住了，后边的就不执行了
    //        $redis->subscribe(['lyj.lyj'], function ($message) {
    //            echo $message.'lyj';
    //        });
    // 正则表达式订阅，并不完全是正则表达式
    //        $redis->psubscribe(["test*"], function ($message, $channel) {
    //            echo $message.'*';
    //        });
}
```

```php
// 正常使用，与命令行中使用没有任何区别，你使用的方法都会转成实例中的方法

```

