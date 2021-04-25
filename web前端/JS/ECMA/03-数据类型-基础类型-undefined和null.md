#### 一、undefined

1.定义

​	一般程序语言中，都会有一个值表示空，在C语言中该空值是null。但js的设计者认为：首先js中的数据有原始类型和引用类型，仅设计一个空值不够用。其次最初的js版本没有错误处理机制，发生数据类型不匹配的时候会默认转换类型，不容易发现错误。所以设计2个空值，一个null，转成数值时为0，一个undefined，转成数值时为NAN。

2.出现场景

- 已声明未赋值的变量
- 获取对象不存在的属性
- 无返回值的函数执行结果
- 函数的参数没有传入
- void（0）

```javascript
var a;
typeof a; // undefined

var b = {};
typeof b.b;// undefined

function c(){};
typeof c();// undefined

function d(x){return x;}
typeof d();// undefined

typeof void(0);// undefined
```

3.类型转换

```javascript
var b = Boolean(undefined); // false

var n = Number(undefined);// NaN

var s = String(undefined);// 'undefined'
```

4.类型检测

​	使用`typeof`操作符鉴别即可，由于undefined不是一个关键字，在某些情况下会被重新赋值，当成一个普通变量使用。

```javascript
typeof undefined; // undefined
typeof 'undefined';// string

typeof ddd; // undefined，未定义的变量只能使用typeof检测，除此之外，全都会报错

var undefined = 10;
console.log(undefined);// undefined，在顶层代码中不会被重写

function test(){
    var undefined = 10;
    console.log(undefined);// 10，在局部作用域中被重写了
}
```

#### 二、null

1.定义

​	用来表示空值，该值就是null。是js中的关键字，逻辑上看，null用来表示空对象指针，也就是没有对象需要让你指的地址。

2.出现场景

​	本来需要返回对象的地方，结果没有对象要返回了，所以给你返回一个null。

```javascript
var o = document.getElementById('test'); // test这个元素并不存在，所以给你返回null

```

3.类型转换

```javascript
var b = Boolean(null);// false

var n = Number(null);// 0

var s = String(null);// 'null'
```

4.类型鉴别

​	使用`typeof`操作符并不能鉴别出来，因为null这个类型二进制前3位是0，所以会鉴别成`object`类型。最好的鉴别方式是它自己`===`它自己

```javascript
typeof null;// object

null == null; // true
undefined == null; // true

'null' === null;  // 恒等用来鉴别
```

#### 三、undefined和null比较

1.并没有明显的区别，两者都是用来描述空值的。

2.undefined用来描述基本类型的空值，null用来描述引用类型的空值。

3.两者比较（==）是相等的，都是空值。

4.类型转换的时候有些许差异。

```javascript
// 用来判断是否存在
if(typeof a == 'undefined' || a === null){
	alert('变量不存在！');
}

// 用来判断变量不能为空
if(typeof a == 'undefined' || !a){
   alert('变量不能为空！');
}

// 用来判断变量是否是对象
if(typeof a == 'undefined' || a === null || typeof a != 'object'){
   alert('变量必须是对象类型');
}

// 用来判断变量是否是简单类型
if(typeof a == 'undefined' || a === null || typeof a == 'object'){
   alert('变量必须是简单类型');   
}
```

















