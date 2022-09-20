<img src="20220708Iterator/Drawing.png" alt="php 可遍历和迭代器" style="zoom:50%;" />

#### 一、RecursiveIterator

​		实现该接口的5个方法，又新增两个循环遍历的东西。

````php
class RecursiveIterator implements Iterator
{
    /* 方法 */
    public getChildren(): ?RecursiveIterator
    public hasChildren(): bool
}
````

```php
class MyRecursiveIterator implements RecursiveIterator 
{ 
    protected $arr;      //存放数据
    protected $index;  //存放当前指针

    public function __construct(array $arr) 
    { 
        $this->arr = $arr; 
    } 
    //判断是否有子元素 
    public function hasChildren() 
    {     
        return is_array($this->arr[$this->index]); 
    }
    //返回子元素的迭代器实例
    public function getChildren() 
    { 
        return new self($this->arr[$this->index]);
    }
    //验证指针是否越界
    public function valid() 
    { 
        return isset($this->arr[$this->index]); 
    }
    //返回当前指针指向数据
    public function current() 
    { 
        return $this->arr[$this->index]; 
    }
    //指针指向下一个元素
    public function next() 
    { 
        $this->index++; 
    } 
    //重置指针
    public function rewind() 
    {     
        $this->index = 0; 
    } 
    //返回当前key值
    public function key() 
    { 
        return $this->index; 
    }
} 

function recursive(MyRecursiveIterator $container,$i=0){
    foreach ($container as $c => $v) {
        echo str_repeat("\t",$i);//缩进
        if ($container->hasChildren()) { //判断是否有子元素
            echo "第{$c}个元素含有子元素:".PHP_EOL; 
            recursive($container->getChildren(),$i+1); //递归调用
        } else { 
            echo "第{$c}个元素：$v".PHP_EOL; 
        }
    }
}

recursive(new MyRecursiveIterator(array(
    0, 1, 2, array(1, 2), array(1, array(1, 2, 3))
)));


```





















