```php
Route::any('/abc', function (\Illuminate\Http\Request $request) {

    // 1、设置一个数据
//    $request->session()->put('username',null);
//    $request->session()->put('user',['id'=>1,'name'=>'test']);
//    $request->session()->put('user.age',33);
//    $request->session()->put('student.id',1);

    // 2、往数组中push数据
    $request->session()->push('student','liuyajie1');
    $request->session()->push('student','liuyajie2');

    // 3、闪存数据，只能用一次
    // 原理非常简单，放入到指定的键上
    // 下次请求结束之前，依然可用
    // 下次请求结束之后，删除它就行
    $request->session()->flash('username','abc');
});

Route::any('/efg', function (\Illuminate\Http\Request $request) {
    // 1、获取数据
    var_dump($request->session()->get('username', 'default'));
    // 获取数据，之后删除它
    var_dump($request->session()->pull('student', 'default'));
    // 获取所有的数据
    var_dump($request->session()->all());

    // 2、是否有这个数据，不论是不是null
    var_dump($request->session()->exists('username'));
    // 有这个数据并且不为null
    var_dump($request->session()->has('username'));

    // 3、删除指定的数据
    $request->session()->forget('student');
    // 删除所有数据
//    $request->session()->flush();

    // 维持住闪存中的数据，意思是这一次先不删除
    // 维持住所有的数据
//    $request->session()->reflash();
    // 维持住指定的数据
//    $request->session()->keep(['username']);
});
```

