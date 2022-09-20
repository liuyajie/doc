```php
// 像数组一样访问，可以使用count函数
class MyClass implements ArrayAccess, Countable
{
    private $string;

    public function __construct($string)
    {
        $this->string = $string;
    }

    public function offsetSet($offset, $value)
    {
        if (is_int($offset) && $offset < strlen($this->string) && $offset >= 0) {
            $this->string = substr_replace($this->string, $value, $offset, 0);
        } else {
            throw new Exception('设置失败，已经越界了！', 500);
        }
    }

    public function offsetUnset($offset)
    {
    }

    public function offsetExists($offset): bool
    {
        return is_int($offset) && $offset < strlen($this->string);
    }

    public function offsetGet($offset)
    {
        return $this->string[$offset];
    }

    public function count(): int
    {
        return strlen($this->string);
    }

}
```

