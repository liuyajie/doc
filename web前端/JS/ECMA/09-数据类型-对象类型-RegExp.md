#### 一、Boolean

1.定义

​	布尔Boolean类型，表示逻辑实体真或假，只有两个值`true`和`false`。它是简单类型，也有对应的复杂类型（引用类型）。

```javascript
// 简单类型
var b1 = true;
typeof b1; // 'boolean'

var c1 = Boolean(true);
typeof c1; // 'boolean'

b1 == c1;// true
b1 === c1;// true

// 引用类型
var b2 = new Boolean(true);
typeof b2; // 'object'

var c2 = new Object(true);
typeof c2;// 'object'

b2 == c2; // false
b2 === c2; // false

b1 == b2;// true
b1 === b2; // false
```

2.出现场景

- 出现在关系表达式中，在for、if、while语句中常出现。关系表达式，一定会返回boolean类型。
- 出现在逻辑运算符中，在for、if、while语句中常出现。逻辑表达式，`！`逻辑非一定会返回boolean类型，`&&` 和 `|| `不一定返回boolean类型。

```js
// 出现在关系表达式中，因为关系表达式总是返回boolean类型
var a = 1;
var b = 2;
if(a < b){
	alert('a<b');
}

// 出现在逻辑表达式中，逻辑非一定返回Boolean类型
if(!a){
   alert('a有问题');
}

```

3.类型转换

- 假值（false）

  ```js
  Boolean(undefined); // false
  Boolean(null); // false
  Boolean(0); // false
  Boolean(-0); // false
  Boolean(NaN);// false
  Boolean(''); // false
  Boolean(false); // false
  ```

- 真值（true）

```js
// 除了以上7种，其余全都可以转成真值，有一些特殊的列出来
Boolean(' '); // true 空格字符串
Boolean('0') // true 字符串0
Boolean({}); // true 空对象
Boolean([]); // true 空数组

var b = new Boolean(false);
Boolean(b); // true 假值的包装对象
var b = new Boolean(true);
Boolean(b); // true 真值的包装对象
var b = new Boolean(null);
Boolean(b); // true 引申出来的其他类型转成boolean包装对象

Boolean(true); // true 它自己
```

4.实例方法

Boolean对象是布尔值对应的包装对象，继承了Object对象的通用方法。

toString()

​	返回Boolean值对应的字符串值，‘true’和‘false’

toLocaleString()

​	返回Boolean值对应的字符串值，‘true’和‘false’

valueOf()

​	返回Boolean值的原始布尔值，true和false

```js
true.toString();// string 'true'
true.toLocaleString();// string 'true'
true.valueOf();// boolean true

false.toString();// string 'false'
false.toLocaleString();// string 'false'
false.valueOf();// boolean true
```











