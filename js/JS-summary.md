## forin和forof的区别

- for…of 遍历获取的是对象的键值，for…in 获取的是对象的键名；

- for… in 会遍历对象的整个原型链，性能非常差不推荐使用，而 for … of 只遍历当前对象不会遍历原型链；

- 对于数组的遍历，for…in 会返回数组中所有可枚举的属性(包括原型链上可枚举的属性)，for…of 只返回数组的下标对应的属性值；

**总结：** for...in 循环主要是为了遍历对象而生，不适用于遍历数组；for...of 循环可以用来遍历数组、类数组对象，字符串、Set、Map 以及 Generator 对象。

```javascript
let values = ['00', { key1: '11' }];
values.name = 'haha'; 
for (let value in values) { console.log(value) }; // 0 1 name
for (let value of values) { console.log(value) }; // 00 { key1: '11'}
```

## 匿名函数和匿名类

```javascript
function myFunction(a, b) {return a * b; // 非匿名函数定义
const x = function (a, b) {return a * b}; // 匿名函数                        
```

```javascript
// 匿名类
const Foo = class {
  constructor() {}
  bar() {
    return 'Hello World!';
  }
};

const instance = new Foo();
instance.bar();  // "Hello World!"
Foo.name;        // "Foo"

```

```javascript
// 非匿名类，当需要在类定义体重使用当前类时
const Foo = class NamedFoo {
  constructor() {}
  whoIsThere() {
    return NamedFoo.name;
  }
}
const bar = new Foo();
bar.whoIsThere();  // "NamedFoo"
NamedFoo.name;     // ReferenceError: NamedFoo is not defined
Foo.name;          // "NamedFoo"
```

## js数据类型

https://juejin.cn/post/6844903854882947080#heading-21

js数据类型是只有程序在运行时，根据等号右边的值确定的。js是动态语言，变量的数据类型可以变化

js数据类型分为两类：

- 原始数据类型：number string boolean undefined null Symbol
- 引用数据类型：object Array Date RegExp Function Map Set
- 特殊引用类型(包装类型)：Boolean Number String

原始数据类型保存在栈中，引用类型的值保存在堆中。通常情况下，栈空间都不会设置太大，主要用来存放一些原始类型的小数据。而引用类型的数据占用的空间都比较大，所以这一类数据会被存放到堆中，堆空间很大，能存放很多大的数据，不过缺点是分配内存和回收内存都会占用一定的时间。

注意：

1. symbol不能参与运算，例如Symbol() + '123'会抛出异常

**包装类型和原始类型的区别**

> 以string举例，字符串存储在字符串常量池中，字符串字面量直接指向常量池的字符串地址，new String 会先在堆上分配空间，然后指向字符串常量池的字符串地址。

```javascript
true === new Boolean(true); // false
123 === new Number(123); // false
'ConardLi' === new String('ConardLi'); // false
console.log(typeof new String('ConardLi')); // object
console.log(typeof 'ConardLi'); // string

// 不能在为基本类型添加属性和方法。
var name = 'ConardLi'
name.color = 'red';
console.log(name.color); // undefined
```

在 JavaScript 中，基本类型是没有属性和方法的，但是为了便于操作基本类型的值，在调用基本类型的属性或方法时 JavaScript 会在后台隐式地将基本类型的值转换为对象

```javascript
const a = "abc";
a.length; // 3
a.toUpperCase(); // "ABC"
```

**number**

二进制、八进制、十进制、十六进制。

```js
var num1 = 07; // 八进制
var num2 = 0x15; // 十六进制
var num3 = Number.MAX_VALUE; // 数字型的最大值
var = num4 = Number.MIN_VALUE; // 数字型的最小值
alert(Infinity); // 无穷大，大于任何数值
alert(-Infinity); // 无穷小，小于任何数值
alert(NaN); // Not a number，代表非数值
```

**string字符串型**

嵌套字符串，外双内单或外单内双

```js
var string1 = "num_num_'num'_num";
```

| 转义符 | 解释说明             |
| ------ | -------------------- |
| \n     | 换行，n 表示newline  |
| \\\    | 斜杠\                |
| \\'    | 单引号               |
| \\"    | 双引号               |
| \\t    | tab 缩进             |
| \\b    | 空格，b是blank的意思 |

**undefined 和null**

null

表示被赋值过的对象，刻意把一个对象赋值为`null`，故意表示其为空，不应有值。

undefined

表示“缺少值”，即此处应有一个值，但还没有定义，

如果一个对象的某个属性值为`undefined`，这是不正常的，如`obj.name=undefined`，我们不应该这样写，应该直接`delete obj.name`。

`undefined`转为数值时为`NaN`(非数字值的特殊值)

`JavaScript`是一门动态类型语言，成员除了表示存在的空值外，还有可能根本就不存在（因为存不存在只在运行期才知道），这就是`undefined`的意义所在。对于`JAVA`这种强类型语言，如果有`"undefined"`这种情况，就会直接编译失败，所以在它不需要一个这样的类型。

```js
var variable；
console.log(variable); // undefined
console.log(variable + 'hello'); // undefinedhello
console.log(variable + 1); //NaN

var vari = null;
console.log(vari); // null
console.log(vari + " hello"); // nullhello
console.log(vari + 1); // 1
```

**转换为字符串**

| 方式             | 说明                         | 案例                             |
| ---------------- | ---------------------------- | -------------------------------- |
| toString()       | 转成字符串                   | var num=1; alert(num.toString()) |
| String()强制装换 | 转成字符串                   | var num=1; alert(num.String())   |
| 加号拼接字符串   | 和字符串拼接的结果都是字符串 | var num=1; alert(num+"string")   |

**转换为数字型**

| 方式                | 说明                         | 案例             |
| ------------------- | ---------------------------- | ---------------- |
| parseInt(string)    | 将string类型转换为整数数值型 | parseInt('78')   |
| parseFloat(string)  | 将string类型转成浮点数值型   | parseFloat('78') |
| Number()强制转换    | 将string转换为数值型         | Number('78')     |
| js隐式转换（- * /） | 利用算数运算隐式转换为数值型 | '12' - 0         |

**转换为布尔型**

| 方式      | 说明               | 案例            |
| --------- | ------------------ | --------------- |
| Boolean() | 其他类型转成布尔型 | Boolean('true') |

- 代表空、否定的值会被转换为false，如‘’，0、NaN、null、undefined。其余值都会转换为true

## 数据类型转换规则

### valueOf

如果对象存在任意原始值，它就默认将对象转换为表示它的原始值，如果对象是引用值，而且大多数对象无法真正表示为一个原始值，因此默认的valueOf( )方法简单地返回对象本身，而不是返回一个原始值。

数组、函数、和正则表达式简单的继承了这个默认方法，调用这些类型的实例的valueOf( )方法只是简单返回对象本身。

日期类定义的valueOf( )方法会返回它的一个内部表示：1970年1月1日以来的毫秒数。  

```javascript
new Date(1970,0,1).toString();  //=>  "Thu Jan 01 1970 00:00:00 GMT+0800 (CST)" 
[1,2].valueOf();  //=>  [1,2]  
(function (){}).valueOf();   //=> function (){}
/\d/.valueOf();    //=>  /\d/  
new Date().valueOf();   //=> 1502941383029
({}).valueOf(); //=> {}
```

### 类型转换规则

- 强制转换（显示转换）：Number()  parseInt()  String()  Boolean()

- 自动转换（隐式转换）

  比较运算（==、!=、>、<）、if、while需要布尔值地方
  算术运算（+、-、*、/、%）

从引用类型到基本类型的转换，会遵循`ECMAScript规范`规定的`toPrimitive`原则，一般会调用引用类型的`valueOf`和`toString`方法。一般转换成不同类型的值遵循的原则不同，例如：

- 引用类型转换为`String`类型
  - 如果对象具有toString( )方法，则调用这个方法。如果返回一个原始值，JavaScript将这个值转换为字符串（如果本身不是字符串的话），并返回这个字符串结果。 
  -  如果对象没有toString( )方法，或者这个方法并不返回一个原始值，那么JavaScript会调用valueOf( )方法。如果存在这个方法，则JavaScript调用它。如果返回值是原始值，JavaScript将这个值转换为字符串（如果本身不是字符串的话），并返回这个字符串结果。  
  - 否则，JavaScript无法从toString()或者valueOf( )获得一个原始值，因此这时它将抛出一个类型错误异常。 
- 引用类型转换为`Number`类型
  - 如果对象具有valueOf( )方法，返回一个原始值，则JavaScript将这个原始值转换为数字（如果需要的话）并返回这个数字。  
  - 否则，如果对象具有toString( )方法，返回一个原始值，则JavaScript将其转化并返回。  
  - 否则，JavaScript抛出一个类型错误异常。 
- 转换为boolean：遵循：只有 0 NaN "" null undefined false是假，其余的都是真
  - **!** 一个感叹号是取反，首先将值转化为布尔类型的值，然后取反
  - **!!** 两个感叹号是将其他的数据类型转换为 boolean 类型，相当于Boolean()


若`valueOf`和`toString`都不存在，或者没有返回基本类型，则抛出`TypeError`异常。

| 转换前类型 | 转换前值       | 转换后(Boolean) | 转换后(Number) | 转换后(String)    |
| ---------- | -------------- | --------------- | -------------- | ----------------- |
| Boolean    | true           | -               | 1              | "true"            |
| Boolean    | false          | -               | 0              | "false"           |
| Number     | 123            | true            | -              | "123"             |
| Number     | Infinity       | true            | -              | "Infinity"        |
| Number     | 0              | false           | -              | "0"               |
| Number     | NaN            | false           | -              | “NaN”             |
| String     | ""             | false           | 0              | -                 |
| String     | "123"          | true            | 123            | -                 |
| String     | "123hah"       | true            | NaN            | -                 |
| String     | "hah"          | true            | NaN            | -                 |
| Symbol     | Symbol()       | true            | TypeError      | TypeError         |
| Null       | null           | false           | ==0==          | "null"            |
| Undefined  | undefined      | false           | NaN            | "undefined"       |
| Function   | function(){}   | true            | NaN            | "function(){}"    |
| Object     | {}             | true            | NaN            | "[object Object]" |
| Object     | { foo: '123'}  | true            | NaN            | "[object Object]" |
| Array      | []             | ==true==        | 0              | ""                |
| Array      | ['hah']        | true            | NaN            | "hah"             |
| Array      | ['123', 'hah'] | true            | NaN            | "123,hah"         |

```
// 以下都是数字0
Number()
Number(0)
Number('')
Number('0')
Number(false)
Number(null)
Number([])
Number([0])
```

### 运算符转换

`+`操作符的两边有至少一个`string`类型变量时，两边的变量都会被隐式转换为字符串；其他情况下两边的变量都会被转换为数字

## 数据比较规则

如果是两个值比较是否相等，遵循这个规则：

1、引用类型 和 普通类型比较，引用类型转换为普通类型后再比较；

2、(number string boolean)普通类型和普通类型比较，都转为数字后再比较

3、只要有一个操作数是布尔值，则在比较相等性之前先将其转换为数值

-  == 比较，会进行默认的数据类型转换

  - 对象 == 对象，永远不相等
- 对象 == 字符串 先将对象转换为字符串，然后在进行比较
  - 对象 == 数字 对象先转为数字(Number)
- 数字 == 布尔 布尔类型转换为数字
  - 数字 == 字符串 字符串转换为数字
- 字符串 == 布尔 都转换为数字
  - null和undefined 和其他任何数据类型都不相等，要比较相等性之前，不能将null和undefined转换成其他任何值
- NaN和其他任何类型比较永远返回false（包括和它自己）
- ===绝对比较，如果数据类型不一样肯定不相等

```javascript
null > 0 // false
null == 0 // false
null >= 0 // true
```

```javascript
[] == ![] // true
null == undefined // true
'[object Object]' == {} // true
'1,2,3' == [1, 2, 3] // true
```

```javascript
let flag1 = null || undefined; // flag1 为undefined
let flag2 = null && undefined; // flag2 为null
```

## 判断对象类型

https://mp.weixin.qq.com/s/-mOmsF0hD7sMRYfFvrN76A

1. typeof 操作符返回一个字符串，表示未经计算的操作数的类型。只能判断基本类型 

```
typeof value
```

| 类型      | 结果      |
| --------- | --------- |
| undefined | undefined |
| null      | object    |
| boolean   | boolean   |
| string    | string    |
| symbol    | symbol    |
| function  | function  |
| {}        | object    |
| []        | object    |

```javascript
var f = function g() {
    return 23;
 };
typeof g(); // Uncaught ReferenceError: g is not defined
```

2、instanceof 运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上

例如：`[] instanceof Array`实际上是判断`Array.prototype`是否在`[]`的原型链上

只有引用数据类型（Array，Function，Object）被精准判断，其他（Number，Boolean，String）字面值不能被instanceof精准判断。

```js
console.log(333 instanceof Number);                  // false
console.log(true instanceof Boolean);                // false 
console.log('aaa' instanceof String);                // false  
console.log([] instanceof Array);                    // true
console.log(function(){} instanceof Function);       // true
console.log({} instanceof Object);                   // true
```

3、Object.prototype.toString.call()

> 每一个引用类型都有`toString`方法，默认情况下，`toString()`方法被每个`Object`对象继承。如果，此方法在自定义对象中没有被覆盖，`toString()` 返回 `"[object type]"`，其中`type`是对象的类型。

```
const obj = {};
obj.toString() // [object Object]
```

注意，上面提到了`如果此方法在自定义对象中未被覆盖`，`toString`才会达到预想的效果，事实上，大部分引用类型比如`Array、Date、RegExp`等都重写了`toString`方法。

可以直接调用`Object`原型上未被覆盖的`toString()`方法，使用`call`来改变`this`指向来达到想要的效果。

```js
var toString = Object.prototype.toString;
toString.call(333);          // [object Number]
toString.call("aaa");        // [object String]
toString.call(true);         // [object Boolean]
toString.call([]);           // [object Array]
toString.call(function(){}); // [object Function]
toString.call({});           // [object Object]
toString.call(undefined);    // [object Undefined]
toString.call(null);         // [object Null]

// 甚至于js的内置对象也能被精确判断
toString.call(new Date);     // [object Date]
toString.call(new String);   // [object String]
toString.call(Math);         // [object Math]
```

4、_isAMomentObject 判断是否为moment对象（momentObject.__isAMomentObject）

5、constructor

```javascript
console.log((2).constructor === Number); // true
console.log((true).constructor === Boolean); // true
console.log(('str').constructor === String); // true
console.log(([]).constructor === Array); // true
console.log((function() {}).constructor === Function); // true
console.log(({}).constructor === Object); // true
```

## 浅拷贝和深拷贝的区别

https://segmentfault.com/a/1190000018874254

https://segmentfault.com/a/1190000020255831

赋值：当我们把一个对象赋值给一个新的变量时，赋的其实是该对象的在栈中的地址，而不是堆中的数据。也就是两个对象指向的是同一个存储空间。

浅拷贝：是按位拷贝对象，它会创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值；如果属性是内存地址（引用类型），拷贝的就是内存地址 。

深拷贝：将一个对象从内存中完整的拷贝一份出来,从堆内存中开辟一个新的区域存放新对象,且修改新对象不会影响原对象

解构赋值用的是浅拷贝。

|        | 和元数据是否指向同一对象 | 第一层数据为基本数据类型 | 原数据中包含子对象       |
| ------ | ------------------------ | ------------------------ | ------------------------ |
| 赋值   | 是                       | 改变会使原数据一同改变   | 改变会使原数据一同改变   |
| 浅拷贝 | 否                       | 改变不会使原数据一同改变 | 改变会使原数据一同改变   |
| 深拷贝 | 否                       | 改变不会使原数据一同改变 | 改变不会使原数据一同改变 |

### 实现浅拷贝

1、手动开辟新对象并赋值

```javascript
function clone(target) {
    let cloneTarget = {};
    for (const key in target) {
        cloneTarget[key] = target[key];
    }
    return cloneTarget;
};
```

2、Object.assign

3、展开运算符...

4、Array.prototype.concat()

5、Array.prototype.slice()

6、lodash  _.clone

### 实现深拷贝

https://segmentfault.com/a/1190000020255831

1、手动操作

- 如果是原始类型，无需继续拷贝，直接返回
- 如果是引用类型，创建一个新的对象，遍历需要克隆的对象，将需要克隆对象的属性执行**深拷贝后**依次添加到新对象上。
  - 考虑数组 和 普通的对象
  - 考虑循环调用时造成栈溢出，使用map存储当前对象和拷贝对象的对应关系
  - 使用WeakMap
  - 性能优化：使用while循环，而不是forin


```javascript
// 使用while封装forEach循环
function forEach(array, iteratee) {
  let index = -1;
  // 缓存length，避免每次都计算length
  const length = array.length;
  while(++index < length) {
    iteratee(array[index], index);
  }
  return array;
}
function clone(target, map = new WeakMap()) {
    if (typeof target === 'object') {
      // 兼容数组
      let cloneTarget = Array.isArray(target) ? [] : {};
      if (map.get(target)) {
         return map.get(target);
      }
      map.set(target, cloneTarget);
      // for (const key in target) {
      //  cloneTarget[key] = clone(target[key], map);
      // }
      const keys = isArray ? undefined : Object.keys(target);
      forEach(keys || target, (value, key) => {
        if (keys) {
          key = value;
        }
        cloneTarget[key] = clone(target[key], map);
      });
      return cloneTarget;
    } else {
        return target;
    }
};
```

2、转成 JSON 再转回来 `obj2 = JSON.parse(JSON.stringify(obj1))`

- 具有循环引用的对象时，报错
- 当值为函数、`undefined`、或`symbol`时，无法拷贝

3、lodash的_.cloneDeep方法

## 循环引用

https://juejin.cn/post/6844903902081449992

1、自身引用 -- 对象的某个属性引用了对象自身

2、互相引用 -- 两个对象的属性间互相引用对方对象

循环引用造成的问题：

1、JSON.stringify 报错，因为无法将无限引用的对象序列化为 JOSN 字符串

2、对象的深拷贝不能正确处理循环引用/递归爆栈

解决循环引用：

## js运算符优先级

js中数值运算，分母为0不会抛出异常，0/0返回NaN

| 运算符     | 顺序                  |
| ---------- | --------------------- |
| 小括号     | ()                    |
| 一元运算符 | ++ -- !               |
| 算数运算符 | 先 * / % 再 + -       |
| 关系运算符 | > >= < <=             |
| 相等运算符 | ==    !   \===    !== |
| 位运算符   | & ^ \|                |
| 逻辑运算符 | 先 && 再 \|\|         |
| 条件运算符 | ?:                    |
| 赋值运算符 | =                     |
| 运算符     | ,                     |

```javascript
1 && 2 > 1; // true
```

## js浮点数运算出错

```javascript
// js中运算
0.1+0.2 // 0.30000000000000004
```

`JavaScript`使用的是64位双精度浮点数编码，所以它的`符号位`占`1`位，指数位占`11`位，尾数位占`52`位。

在计算机中它是以科学计数法表示的，也就是`1.100110011001100...` X 2-4

![image](https://images-sally.oss-cn-beijing.aliyuncs.com/img/js-%E4%BA%8C%E8%BF%9B%E5%88%B6%E5%AD%98%E5%82%A8%E7%A4%BA%E6%84%8F.png)

1、`符号位`就是标识正负的，`1`表示`负`，`0`表示`正`；

2、`指数位`存储科学计数法的指数；

指数位是负数，该如何保存呢？IEEE标准规定了一个偏移量，对于指数部分，每次都加这个偏移量进行保存，这样即使指数是负数，那么加上这个偏移量也就是正数了。由于JavaScript的数字是双精度数，这里就以双精度数为例，它的指数部分为11位，能表示的范围就是0~2047，IEEE固定**双精度数的偏移量为1023**。


对于上面的0.1的指数位为-4，-4+1023 = 1019 转化为二进制就是：`1111111011`.

3、`尾数位`存储科学计数法后的有效数字；

所以我们通常看到的二进制，其实是计算机实际存储的尾数位。双精度浮点数的小数部分最多只能保留52位，再加上前面的1，其实就是保留53位有效数字，剩余的需要舍去，遵从“0舍1入”的原则。

计算步骤：

比如：以下是十进制小数对应的二进制表示

```
0.1 >> 0.0001 1001 1001 1001…（1001无限循环）
0.2 >> 0.0011 0011 0011 0011…（0011无限循环）
相加 =》 0.0100 1100 1100 ……
转换为十进制 =》 2^-2 + 2^-5 + 2^-6 + ……
```

注：十进制小数转换为二进制转换方法：利用小数部分乘2，取整数部分，直至小数点后为0

| 运算          | 取   | 余   |
| ------------- | ---- | ---- |
| 0.1 * 2 = 0.2 | 0    | 0.2  |
| 0.2 *2 = 0.4  | 0    | 0.4  |
| 0.4 * 2 = 0.8 | 0    | 0.8  |
| 0.8 * 2 = 1.6 | 1    | 0.6  |
| 0.6 * 2 = 1.2 | 1    | 0.2  |
| 0.2 * 2 = 0.4 | 0    | 0.4  |
| ……            | ……   | ……   |

二进制转换为十进制

```
1100.1100-> 2^3 + 2^2 + 2^-1 + 2^-2 -> = 8 + 4 + 0.5 + 0.25 = 12.75
```

## function 中arguments

当不确定function的形参个数时，可以使用arguments，其存储了所有传递过来的实参。

arguments是伪数组，并不是真正意思的数组：

- 具有数组的length属性
- 按照索引的方式进行存储的
- 没有真正数组的一些方法。

```js
// 举例
function fn() {
	console.log(arguments);  // [1, 2, 3, ……(一些原型)]
    console.log(arguments.lenght); // 3
    console.log(arguments[0]); // 1
}

fn(1, 2, 3)
```

## js预解析

声明function的方式有2种

```js
// 1. function关键字
function fn(args) {
	console.log("111");
}

// 2. 变量声明
var fn = function(args) {
    console.log("222");
}
```

1. js引擎运行js分为2步：预解析、代码执行

（1）预解析：js引擎会把js里面所有的var和function全部提到当前作用域的最前面

（2）代码执行：按照代码书写的顺序从上往下执行

2. 预解析分为变量预解析（变量提升）和函数预解析（函数提升）

（1）变量提升：就是把所有的变量声明提升到当前的作用域最前面，只提升声明，不提升赋值

（2）函数提升：把所有的函数声明提升到当前作用域的最前面，不调用函数

```js
// 局部变量和全局变量
(function() {
    // 相当于 var x = 1; y = 1; y前面没有var声明，相当于全局变量
    // 集体声明 var x = 1, y = 1;
    var x = y = 1; 
})();
console.log(y);
console.log(x);

// 输出
1
（报错）
```

## 改变函数内部this指向

如果函数处在非严格模式下，且thisArg的值为null或者undefined，则调用时函数内部的this指向window对象

1. call()

可以用来修改函数运行时的this指向，经常用来做继承

```
fun.call(thisArg, arg1, arg2, ...)
```

thisArg: 当前调用函数this的指向对象

arg1 arg2：传递的其他参数

```js
// 一、借用构造函数继承属性
// 1. 父构造函数
function Father(uname, age) {
	// this指向父构造函数的对象实例
	this.uname = uname;
	this.age = age;
}
// 2. 子构造函数
function Son(uname, age) {
	Father.call(this, uname, age);
}
var son = new Son('ldh', 18);

// 二、借用构造函数继承方法
Son.prototype = new Father();
Son.prototype.constructor = Son;
```

实现call方法：

```javascript
Function.prototype.myCall = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError("Error");
  }
  // 获取参数
  let args = [...arguments].slice(1);
  // 判断 context 是否传入，如果未传入则设置为 window
  context = context || window;
  // 将调用函数设为对象的方法
  context.fn = this;
  // 调用函数
  const result = context.fn(...args);
  // 将属性删除
  delete context.fn;
  return result;
}
```

2. apply

经常跟数组有关系

```
fun.apply(thisArg, [argsArray])
```

thisArg: 在fun函数运行时指定的this值

argsArray：传递的值，必须包含在数组里面

返回值就是函数的返回值，因为它就是调用函数

```js
// apply的主要应用，比如可以利用apply借助于Math对象求最大值
var arr = [1, 2, 3, 4];
var max = Math.max.apply(Math, arr);
```

实现apply方法：

```javascript
Function.prototype.myApply = function(context) {
  // 判断调用对象是否为函数
  if (typeof this !== "function") {
    throw new TypeError("Error");
  }
  let result = null;
  // 判断 context 是否存在，如果未传入则为 window
  context = context || window;
  // 将函数设为对象的方法
  context.fn = this;
  // 调用方法
  if (arguments[1]) {
    result = context.fn(...arguments[1]);
  } else {
    result = context.fn();
  }
  // 将属性删除
  delete context.fn;
  return result;
};
```

3. bind

bind()方法不会调用函数，但是能改变函数内部this指向，返回由指定的this值和初始参数改造的原函数拷贝

```
// thisArg: 在fun函数运行时指定的this值
// arg1, arg2: 传递的其他参数
fun.bind(thisArg, arg1, arg2, ……)
```

实现bind方法：

```javascript
Function.prototype.myBind = function(context) {
  // 判断调用对象是否为函数
  if (typeof this !== "function") {
    throw new TypeError("Error");
  }
  // 获取参数
  var args = [...arguments].slice(1),
    fn = this;
  return function Fn() {
    // 函数作为构造函数时，传入当前函数的 this 给 apply 调用，其余情况都传入指定的上下文对象。
    return fn.apply(
      this instanceof Fn ? this : context,
      args.concat(...arguments)
    );
  };
};
```

## js各种不同继承方式

https://github.com/mqyqingfeng/Blog/issues/16

1. 原型链继承

2. 借用构造函数

   ```javascript
   function Parent () {
       this.names = ['kevin', 'daisy'];
   }
   
   function Child () {
       Parent.call(this);
   }
   ```


## 类

类的本质是function，可以认为是构造函数的另一种写法。ES6的类其实是函数的语法糖。

类中可以写constructor、写方法、赋值语句（不能加let const var 等）

```
a = 1; // 类中直接写赋值语句，相当于给类的实例对象添加属性 a, 值为1
```

constructor

- 需要初始化一些数据时才需要写constructor。
- 当使用extends继承时，子类要么不写constructor，则自动执行父类的构造函数。当子类有constructor时，子类的构造函数中必须先写super(props)，作用是创建子类的实例时，能够把props通过super执行父类的构造函数
- constructor里面的this指向实例对象，方法里面的this指向这个方法的调用者

- 类实质上 是一个对象，它的方法放在了类的实例对象proto上，供实例使用

语法糖：表示一种便捷写法。简单理解，有2种方法可以实现同样的功能，但是一种写法更加清晰方便，那么这个方法就是语法糖。

## 函数

函数内形参不能删除

1. 函数：普通函数、对象的方法、构造函数、绑定事件函数、定时器函数、立即执行函数

   注：立即执行函数   (function () {})()    或者  (function() {} ())

2. 异步操作：
   - 定时器
   - 事件绑定
   - AJAx中一般采取异步操作（也可以同步）
   - 回调函数可以理解为异步（不是严谨的异步操作）

3. 函数内this指向

| 调用方式     | this指向                                   |
| ------------ | ------------------------------------------ |
| 普通函数调用 | window                                     |
| 构造函数调用 | 实例对象，原型对象里面的方法也指向实例对象 |
| 对象方法调用 | 该方法所属对象                             |
| 事件绑定方法 | 绑定事件对象                               |
| 定时器函数   | window                                     |
| 立即执行函数 | window                                     |

4. 高阶函数：符合下面2个规范中的任意一个，那该函数就是高阶函数

   1. 若A函数，接收的参数是一个函数，那么A就可以称之为高阶函数
   2. 若A函数，调用的返回值依然是一个函数，那么A就可以称之为高阶函数

   常见的高阶：Promise、setTimeOut

5. 函数柯里化：通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式

6. 递归函数

```js
// 1. 利用递归函数求1-n的阶乘
function fn(n) {
    if (n == 1) {
        return 1;
    }
    return n * fn(n-1);
}
// 2. 求斐波那契数列（兔子序列） 1 1 2 3 5 8 13
// 用户输入数字n，求出这个数字n对应的兔子序列值
function fn(n) {
    if (n == 1 || n == 2) {
        return 1;
    }
   return fn(n - 1) + fn(n - 2);
}
```

## 闭包

https://segmentfault.com/a/1190000023356598

https://juejin.cn/post/6869621390849687566

闭包：当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。闭包是指有权访问另一个函数作用域中变量的函数。"定义在一个函数内部的函数"。

> 作用域：根据名称查找变量的一套规则。分为三种类型：全局作用域；函数作用域；块作用域。
>
> 作用域链：当不同的作用域 圈套在一起时，就形成了作用域链。注意的是，查找方向是从内到外的。

```javascript
function foo() {
    var a = 1; // a 是一个被 foo 创建的局部变量
    function bar() { // bar 是一个内部函数，是一个闭包
        console.log(a); // 使用了父函数中声明的变量
    }
    return bar;
}
const myFoo = foo();
myFoo();
```

`foo()` 函数执行后，正常情况下 `foo()` 的整个内部作用域被销毁，占用的内存被回收。但是现在的 `foo`的内部作用域 `bar()` 还在使用，所以不会对其进行回收。bar() 依然持有对改作用域的引用，这个引用就叫做闭包。

定时器，事件监听器，`Ajax`请求，跨窗口通信，`Web Workers`或者其他异步或同步任务中，只要使用回调函数，实际上就是闭包。

**注意：**闭包容易导致内存泄漏。闭包会携带包含它的函数作用域，因此会比其他函数占用更多的内存。

**闭包用途：**

- 创建私有变量和方法。通过使用闭包，可以通过在外部调用闭包函数，从而在外部访问到函数内部的变量
- 柯里化函数：利用闭包可以把参数分成多次传参
- 实现防抖或者节流函数
- 实现缓存结果。使已经运行结束的函数上下文中的变量对象继续留在内存中，因为闭包保留了这个变量对象的引用，所以这个变量对象不会被回收。

## 浏览器地址组成

举例：`http://127.0.0.1:8848/web2012/index.html#main1?wd=xxx&name=sds`

第一部分：http

数据传输协议。协议是一种规范或标准，要按照该标准进行数据传输，若不加，浏览器不会执行数据传输。

第二部分：127.0.0.1:8848

主机：IP/域名 + 端口号   127.0.0.1 IP/域名    8848 端口号：区分同一台电脑上多个服务

第三部分：web2012/index.html

路由地址：通过该地址映射对应服务器资源

第四部分：#main1

锚点：快速定位文档内数据

第五部分：wd=xxx&name=sds

参数：由键值对组成，且用等号进行连接，多个参数用&符号进行分隔

## 类初始化时顺序

赋值且有值 > constructor

以下例子中先执行b的初始化，然后是d，然后执行constructor

```javascript
class Test {
  constructor() {
    this.a = 1;
  }
   b = 2;
   c;
   d = () => {};
}
```

## gataway

https://blog.csdn.net/rain_web/article/details/102469745

## js执行机制

除了广义的同步任务和异步任务，分的更加精细一点：

- macro-task(宏任务)：包括整体代码script，setTimeout，setInterval
   - micro-task(微任务)：Promise，process.nextTick，`process.nextTick` 比`Promise.then()`先执行
      不同的任务会进入到不同的event queue。比如setTimeout和setInterval会进入相同的Event Queue。


执行顺序：

1、第一次循环，执行调用栈中同步代码 + promise的实例部分
2、执行微任务，直到清空微任务队列后，第一次循环结束
3、进入第二轮，将放在event queue的任务放入到调用栈执行

以下执行结果是：1,7,6,8,2,4,3,5,9,11,10,12,1111,2222，注意每一轮只有一个setTimeout的回调到调用栈

```javascript
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    });
    setTimeout(() => { console.log('1111');}, 0)
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    });
    setTimeout(() => { console.log('2222');}, 0)
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})
```

## 内存泄漏

https://segmentfault.com/a/1190000020231307

出现内存泄漏的场景：

1、意外导致的全局变量（可被eslint报错，然后排除）

```javascript
// 在全局作用域下定义
function count(number) {
  // basicCount 相当于 window.basicCount = 2;
  basicCount = 2;
  return basicCount + number;
}
```

2、被遗忘的计时器 setInterval，需要使用clearInterval清除

3、被遗忘的事件监听器，需要使用removeEventListener清除监听

4、ES6 Set Map，当Set成员或Map键是引用类型。手动释放，或者使用WeakSet WeakMap

```javascript
let map = new Set();
let value = { test: 22};
map.add(value);
// 手动释放
map.delete(value);
value = null;

let map = new Map();
let key = new Array(5 * 1024 * 1024);
map.set(key, 1);
// 手动释放
map.delete(key);
key = null;
```

5、被遗忘的订阅发布事件监听器

6、被遗忘的闭包

```javascript
function closure() {
  const name = 'xianshannan'
  return () => {
    return name
      .split('')
      .reverse()
      .join('')
  }
}
const reverseName = closure()
// 若不执行，name一直存在，就会造成内存泄漏
reverseName();
```

## 严格模式

1. 不允许使用未声明的变量

2. 不允许使用delete删除变量、对象、函数

3. 不允许变量重名

   ```
   "use strict";
   function x(p1, p1) {};   // 报错
   ```

4. 不允许使用八进制

5. 不允许使用转义字符

6. 不允许对只读属性赋值

7. 不允许对一个使用getter方法读取的属性进行赋值

8. 不允许删除一个不允许删除的属性

   ```
   "use strict";
   delete Object.prototype; // 报错
   ```

9. 变量名不能使用 "eval" 字符串

10. 变量名不能使用 "arguments" 字符串:

11. 禁止this关键字指向全局对象。

    ```js
    function f(){
        return !this;
    } 
    // 返回false，因为"this"指向全局对象，"!this"就是false
    
    function f(){ 
        "use strict";
        return !this;
    } 
    // 返回true，因为严格模式下，this的值为undefined，所以"!this"为true。
    
    // 因此，使用构造函数时，如果忘了加new，this不再指向全局对象，而是报错。
    function f(){
        "use strict";
        this.a = 1;
    };
    f();// 报错，this未定义
    ```

## 遍历类数组

（1）将数组的方法应用到类数组上，这时候就可以使用`call`和`apply`方法，如：

```javascript
function foo(){ 
  Array.prototype.forEach.call(arguments, a => console.log(a))
}
```

（2）使用Array.from方法将类数组转化成数组：‌

```javascript
function foo(){ 
  const arrArgs = Array.from(arguments) 
  arrArgs.forEach(a => console.log(a))
}
```

（3）使用展开运算符将类数组转化成数组

```javascript
function foo(){ 
    const arrArgs = [...arguments] 
    arrArgs.forEach(a => console.log(a)) 
}
```

## 尾调用优化

尾调用指的是函数的最后一步调用另一个函数。代码执行是基于执行栈的，所以当在一个函数里调用另一个函数时，会保留当前的执行上下文，然后再新建另外一个执行上下文加入栈中。使用尾调用的话，因为已经是函数的最后一步，所以这时可以不必再保留当前的执行上下文，从而节省了内存，这就是尾调用优化。但是 ES6 的尾调用优化只在严格模式下开启，正常模式是无效的。

## MVC 和 MVVM

## MVVM 和 MVC

https://www.cnblogs.com/ranyonsue/p/12090647.html

**MVC:** Model(模型) - View(视图) - Controller(控制器)，View传送指令到Controller，Controller完成业务逻辑后改变Model状态，Model将新的数据发送至View,用户得到反馈。所有通信都是单向的。

优点：

1、耦合性低。视图层和业务层分离，这样就允许更改视图层代码而不用重新编译模型和控制器代码

2、重用性高。MVC模式允许使用各种不同样式的视图来访问同一个服务器端的代码，因为多个视图能共享一个模型

3、可维护性高。分离视图层和业务逻辑层也使得WEB应用更易于维护和修改。

4、部署块。使用MVC模式使开发时间得到相当大的缩减，它使程序员（Java开发人员）集中精力于业务逻辑，界面程序员（HTML和JSP开发人员）集中精力于表现形式上。

缺点：

1、增加系统结构和实现的复杂性。对于简单的界面，严格遵循MVC，使模型、视图与控制器分离，会增加结构的复杂性，并可能产生过多的更新操作，降低运行效率。

2、视图与控制器间的过于紧密的连接。视图与控制器是相互分离，但却是联系紧密的部件，视图没有控制器的存在，其应用是很有限的，反之亦然，这样就妨碍了他们的独立重用。

3、视图对模型数据的低效率访问。依据模型操作接口的不同，视图可能需要多次调用才能获得足够的显示数据。对未变化数据的不必要的频繁访问，也将损害操作性能。

**MVVM**：Model-View-ViewModel。把`MVC`中的`Controller`改变成了`ViewModel`。`View`的变化会自动更新到`ViewModel`,`ViewModel`的变化也会自动同步到`View上`显示。开发人员不用操作DOM，只用关注业务逻辑，与MVC的主要区别，使用VM调度者实现了数据的双向绑定

优点：

1、低耦合。视图（View）可以独立于Model变化和修改，一个ViewModel可以绑定到不同的"View"上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。

2、可重用性。你可以把一些视图逻辑放在一个ViewModel里面，让很多view重用这段视图逻辑。

3、独立开发。开发人员可以专注于业务逻辑和数据的开发（ViewModel），设计人员可以专注于页面设计

4、可测试。界面素来是比较难于测试的，而现在测试可以针对ViewModel来写。

缺点：

1、数据绑定也使得bug很难被调试

2、数据双向绑定不利于代码重用

3、一个大的模块中model也会很大，虽然使用方便了也很容易保证数据的一致性，但是长期持有，不释放内存就造成话费更多的内存。

## 防抖和节流

- 防抖: n 秒后在执行该事件，若在 n 秒内被重复触发，则重新计时

  ```javascript
  function debounce(fn, delay) {
      let timer; // 维护一个 timer
      return function () {
          let _this = this; // 取debounce执行作用域的this，否则定时器内的回调this指向window
          let args = arguments;
          if (timer) {
              clearTimeout(timer);
          }
          timer = setTimeout(function () {
              fn.apply(_this, args); // 用apply指向调用debounce的对象，相当于_this.fn(args);
          }, delay);
      };
  }
  ```

- 节流: n 秒内只运行一次，若在 n 秒内重复触发，只有一次生效

  类似控制阀门一样定期开放的函数，也就是让函数执行一次后，在某个时间段内暂时失效，过了这段时间后再重新激活（类似于技能冷却时间）

  ```javascript
  // 定时器版本
  function throttle(fn, delay) {
      let timer;
      return function () {
          let _this = this;
          let args = arguments;
          if (timer) {
              return;
          }
          timer = setTimeout(function () {
              fn.apply(_this, args);
              timer = null; // 在delay后执行完fn之后清空timer，此时timer为假，throttle触发可以进入计时器
          }, delay)
      }
  }
  
  // 时间戳版本
  function throttle(func, delay) {
      var last = 0;
      return function () {
          var now = Date.now();
          if (now >= delay + last) {
              func.apply(this, arguments);
              last = now;
          } else {
              return;
          }
      }
  }
  ```

## 懒加载

图片的加载是由src引起的，当对src赋值时，浏览器就会请求图片资源。根据这个原理，我们使用HTML5 的data-xxx属性来储存图片的路径，在需要加载图片的时候，将data-xxx中图片的路径赋值给src，这样就实现了图片的按需加载，即懒加载。
注意：data-xxx 中的xxx可以自定义，这里我们使用data-src来定义。
使用原生JavaScript实现懒加载：
（1）window.innerHeight 是浏览器可视区的高度
（2）document.body.scrollTop || document.documentElement.scrollTop 是浏览器滚动的过的距离
（3）imgs.offsetTop 是元素顶部距离文档顶部的高度（包括滚动条的距离）
（4）图片加载条件：img.offsetTop < window.innerHeight + document.body.scrollTop;

![img](https://images-sally.oss-cn-beijing.aliyuncs.com/img/%E6%87%92%E5%8A%A0%E8%BD%BD%E5%8E%9F%E7%90%86%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

```html
<div class="container">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
</div>
<script>
var imgs = document.querySelectorAll('img');
function lozyLoad(){
		var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
		var winHeight= window.innerHeight;
		for(var i=0;i < imgs.length;i++){
			if(imgs[i].offsetTop < scrollTop + winHeight ){
				imgs[i].src = imgs[i].getAttribute('data-src');
			}
		}
	}
  window.onscroll = lozyLoad();
</script>
```

## js的内置对象

| 对象      |              |
| --------- | ------------ |
| Arguments | 函数参与集合 |
| Array     | 数组         |
| Boolean   |              |
| Date      |              |
| Error     |              |
| Function  |              |
| Math      |              |
| Number    |              |
| Object    |              |
| Regexp    |              |
| String    |              |

## js全局函数

escape( )、eval_r( )、isFinite( )、isNaN( )、parseFloat( )、parseInt( )、unescape( )

## 哪些操作会造成内存泄漏？

- 第一种情况是由于使用未声明的变量，而意外的创建了一个全局变量，而使这个变量一直留在内存中无法被回收。
- 第二种情况是设置了 setInterval 定时器，而忘记取消它，如果循环函数有对外部变量的引用的话，那么这个变量会被一直留在内存中，而无法被回收。
- 第三种情况是获取一个 DOM 元素的引用，而后面这个元素被删除，由于我们一直保留了对这个元素的引用，所以它也无法被回收。
- 第四种情况是不合理的使用闭包，从而导致某些变量一直被留在内存当中。

## TypeError SyntaxError ReferenceError

TypeErrors get thrown when a value is not of the expected type. JavaScript expected name to be a function since we’re trying to invoke it. It was a string however, so a TypeError gets thrown: name is not a function!

```javascript
let name = "";
console.log(name.reduce());  // TypeError: name.reduce is not a function
```

SyntaxErrors get thrown when you’ve written something that isn’t valid JavaScript, for example when you’ve written the word return as retrun.

```javascript
le name ="";// SyntaxError: Unexpected identifier
```

ReferenceErrors get thrown when JavaScript isn’t able to find a reference to a value that you’re trying to access.

```javascript
console.log(name); // ReferenceError: name is not defined
```









