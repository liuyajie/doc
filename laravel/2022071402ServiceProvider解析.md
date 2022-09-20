```php
// $this->register(App\Providers\Test::class);             添加及时服务
// $this->addDeferredServices(App\Providers\Test::class);  添加延时服务
// 延迟服务比及时服务多一个providers函数，返回当什么实例化时你才需要解析服务提供者，注册需要的服务
// 流程如下
// route => 解析route服务提供者，注册route服务 => 执行route服务提供者的boot方法 => 解析route服务

// 框架启动之前
// 注册及时服务->执行register方法，不执行boot方法，make之后也没有其他操作
// 注册延迟服务->不执register方法，不执行boot方法，make之后把boot放入 回调数组

// 框架启动
// 注册及时服务->执行boot方法
// 注册延迟服务->执行 回调数组

// 框架启动之后
// 注册及时服务->执行register方法，执行boot方法，make之后也没有其他操作
// 注册延迟服务->不执register方法，不执boot方法，make之后才会执行
```

```php
// 发布文件也是在boot里边写的
public function boot()
{
    $this->publishes([
        public_path('web.config') => public_path('web.config1'),
    ], 'hahha');
}
# php artisan vendor:publish 
# 选择你的服务提供者或你们几个所在的组或包
```

```php
# 第三方包加的provider和facade别名放的文件
# \laravel\bootstrap\cache\packages.php

# 也有某些包当你安装它的时候，它会把服务提供者直接注册进去
"extra": {
    "laravel": {
        "providers": [
            "BeyondCode\\DumpServer\\DumpServerServiceProvider"
        ],
        "alias":[
            "Lyj":"BeyondCode\\DumpServer\\DumpFacadeTS"
        ]
    }
}

# 其实质吧，无论是provider还是Facade都是中间人

# StudentProvider 提供 st 到 Student类 的绑定
# 执行boot方法（做一些其他事情）
# 当$app->make('st')时，首先去找StudentProvider是否给我注册进去
# 如果注册了，我直接实例化，没有注册也不影响什么，我再去解析就行了

# Student 静态使用
# 查找 Facade，Facade返回实例的名称
# 当$app->make('st')时，首先去找StudentProvider是否给我注册进去
# 如果注册了，我直接实例化，没有注册也不影响什么，我再去解析就行了
```











