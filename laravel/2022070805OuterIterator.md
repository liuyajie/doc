<img src="20220708Iterator/Drawing.png" alt="php 可遍历和迭代器" style="zoom:50%;" />

#### 一、OuterIterator

​		Iterator下边的子迭代器接口，有点新的小功能。类似于迭代器聚合的功能，传入多个迭代器，然后进行迭代。迭代迭代器用的。

```php
class OuterIterator extends Iterator
{
    // 比Iterator多了一个获取内部的Iterator的方法，其作用是为了迭代迭代器而准备的
    // 实际上并不会使用这个方法，而是使用其下边实现的类
    public function getInnerIterator()
    {
        
    }
}
```

#### 二、IteratorIterator

​		实现OuterIterator接口的类

```php
class IteratorIterator implements IteratorIterator
{
    // 比OuterIterator多了一个构造方法，大概意思就是，你给我一个迭代器，然后呢，我可以对要迭代的数据做各种处理
    // 一般很少直接使用这个类，没啥意思的，只是为了知识架构的完整性。
    // 直接使用与最原始的迭代器没啥区别
    public function __construct(Traversable $iterator)
    {
        
    }
    
    // 比Iterator多了一个获取内部的Iterator的方法，其作用是为了迭代迭代器而准备的
    // 实际上并不会使用这个方法，而是使用其下边实现的类
    public function getInnerIterator()
    {
        
    }
}
```

#### 三、FilterIterator

```php
// 该类无法直接被使用，只能被继承
abstract FilterIterator extends IteratorIterator
{
    
    // 它内部做了一些处理，具体的你可以看
    // 当你子类有一个accept方法是，它会遍历时会调用它，发现是true则给你返回，否则给你跳过去
    public function next(){
        //if($this->accept()){
        //     return $this->pos++;
        //}else{
        //      return $this->pos++++;
        //}
    }
}


class User extends FilterIterator
{
    private $userFilter;
    public function __construct(Iterator $iterator , $filter )
    {
        parent::__construct($iterator);
        $this->userFilter = $filter;
    }
    
    public function accept()
    {
        $user = $this->getInnerIterator()->current();
        if( strcasecmp($user['name'],$this->userFilter) == 0) {
            return false;
        }       
        return true;
    }
}

$it = new User(new ArrayIterator([[
    'name' => 'lyj',
    'age'=>'31'
],[
    'name' => 'lyj1',
    'age'=>'31'
]]),'lyj');

foreach ($it as $item) {
    var_dump($item);
}
```

#### 四、CallbackFilterIterator

```php
class CallbackFilterIterator extends FilterIterator {
    public __construct(Iterator $iterator, callable $callback)
    public accept(): bool
}

# 比上边的方法又更完善了一步
# 更近一步给你安排好了，可以直接写逻辑
new CallbackFilterIterator(new ArrayIterator([[
    'name' => 'lyj',
    'age'=>'31'
],[
    'name' => 'lyj1',
    'age'=>'32'
]]),function($current, $key, $iterator){
    if($current['age'] < 32){
        return true;
    }
    return false;
});
```

#### 五、LimitIterator

```php
# 在IteratorIterator基础上又做了一些额外的功能
# 我们也可以实现的，在valid中实现即可
class LimitIterator extends IteratorIterator {
    private $pos;
    private $offset;
    private $limit;
    public function __construct(Iterator $it,$offset,$limit){
        $this->pos = $offset;
        $this->offset = $offset;
        $this->limit = $limit;
    }
    
    public function valid(){
       if( $this->pos < $this->offset + $this->limit ){
           return true;
       }
        return false;
    }
    
    # 多了一个方法
    public function seek(){
        
    }
}

$it = new LimitIterator(new ArrayIterator([1,2,3,4]),2,1);
foreach($it as $item){
    var_dump($item);
}
```

#### 六、InfiniteIterator

```php
# 这一次搞了一个无限循环
class InfiniteIterator extends IteratorIterator
{
    public function next()
    {
        $this->pos++;
        if(!$this->valid()){
     		$this->pos = 0;       
        }
    }
}

# 自己的实现
class My extends IteratorIterator
{
    public function next()
    {
        parent::next();
        if (!parent::valid()) {
            parent::rewind();
        }
    }
}

# 用法
class MyData
{
    public $a = 'a';
    public $b = 'b';
    public $c = 'c';
}
$it1 = new InfiniteIterator(new ArrayIterator(new MyData()));

$it2 = new LimitIterator($it1,0,14);
```

#### 七、AppendIterator

​		一般有多个迭代器的话，我们直接使用这个

```php
class AppendIterator extends IteratorIterator
{
    
}

$my1 = new MyArr([1,2,3]);
$my2 = new MyArr([4,5,6]);

# 拼接迭代器来着
$its = new AppendIterator();
$its->append($my1);
$its->append($my2);

foreach ($its as $value) {
    var_dump($value);
}
```

以上这些方法，我们都可以自己去实现，但是系统有实现好的，那么我们就直接用呗。

















