## number

### toFixed

可把 Number 四舍五入为指定小数位数的数字

```js
//保留四位小数
var number = 1.23456789;
number = number.toFixed(4);
```

### toPrecision

以指定的精度返回该数值对象的字符串表示，四舍五入到 precision 参数指定的显示数字位数。

```
number.toPrecision(x)
x: 可选。规定要转换为几位数，该参数是 1 ~ 100 之间（且包括 1 和 100 ）的值。如果省略了该参数，则调用方法 toString()，返回原始数字的字符串形式。如果 precison 参数不在 1 和 100 （包括）之间，将会抛出一个 RangeError 。
返回：string
```

```javascript
var num = new Number(13.3714);
var n=num.toPrecision(2); // 13 (string)
```

### parseInt

```javascript
parseInt(value, 进制)
```

parseInt 解析一个字符串，并返回一个整数

### parseFloat

解析一个参数（必要时先转换为字符串）并返回一个浮点数。如果参数字符串的第一个字符不能被解析成为数字,则 parseFloat 返回 NaN。

```javascript
parseFloat('3.14'); // 3.14
```

### isNaN 和 Number.isNaN

`Number.isNaN`确定传递的值是否为 NaN，返回一个布尔值，表示给定的值是否是 NaN。不会自行将参数转换成数字，只有在参数是值为 `NaN` 的数字时，才会返回 `true`

函数 isNaN 接收参数后，会尝试将这个参数转换为数值，任何不能被转换为数值的的值都会返回 true，因此非数字值传入也会返回 true 

```javascript
Number.isNaN('Sally'); // false
Number.isNaN(18); // false
Number.isNaN(NaN); // true
isNaN('Sally'); // true
isNaN(NaN); // true
isNaN(18); // false
isNaN('18'); // false
isNaN(1/0); // false 1/0在js中不会报错，是无穷大，infinity
isNaN(0/0); // true
```

## Array

https://javascript.ruanyifeng.com/stdlib/array.html

### 创建Array对象

new Array 

```js
new Array(); new Array(size); new Array(element0, element1, ..., elementn);
```

Array.from

从一个类似数组或可迭代对象创建一个新的

```js
Array.from(arrayLike[, mapFn[, thisArg]])
```

| 参数         | 描述                          |
| ---------- | --------------------------- |
| arrayLike  | 想要转换成数组的伪数组对象或可迭代对象         |
| mapFn 可选   | 如果指定了该参数，新数组中的每个元素会执行该回调函数  |
| thisArg 可选 | 可选参数，执行回调函数 mapFn 时 this 对象 |
| 返回         | 一个新的数组                      |

```js
console.log(Array.from([1, 2, 3], x => x + x)); // expected output: Array [2, 4, 6]
```

Array.of

创建一个具有可变数量参数的新数组实例，而不考虑参数的数量或类型

```js
// 任意个参数，将按顺序成为返回数组中的元素，返回新的Array
Array.of(element0[, element1[, ...[, elementN]]])
```

```js
// 和Array的区别
Array.of(7);       // [7]
Array.of(1, 2, 3); // [1, 2, 3]

Array(7);          // [ , , , , , , ]
Array(1, 2, 3);    // [1, 2, 3]
```

### Array.isArray(arr)

```js
var arr = [1, 2, 3];
typeof arr // "object"
Array.isArray(arr) // true
```

### toString

```javascript
const arr =[[2,1,4,2], 5,2, [3,2,1], [6,7]];
console.log(arr.toString());
// 2,1,4,2,5,2,3,2,1,6,7
```

### fill

用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。不包括终止索引。

```javascript
arr.fill(value[, start[, end]])
```

### reduce()和reduceRight()

reduce方法和reduceRight方法依次处理数组的每个成员，最终累计为一个值。它们的差别是，reduce是从左到右处理（从第一个成员到最后一个成员），reduceRight则是从右到左（从最后一个成员到第一个成员），其他完全一样。

理解：reduce 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或从未被赋值的元素，接受四个参数：初始值（或者上一次回调函数的返回值），当前元素值，当前索引，调用 reduce 的数组。

total: 默认为数组第0个元素，currentValue默认为数组第1个元素

```js
array.reduce(function(total, currentValue, currentIndex, arr), initialValue)
```

| 参数                                               | 详细           | 描述                |
| ------------------------------------------------ | ------------ | ----------------- |
| function(total, currentValue, currentIndex, arr) | total        | 必需。初始值，或计算结束后的返回值 |
| 必须。用于执行每个数组元素的函数                                 | currentValue | 必须。当前元素           |
|                                                  | currentIndex | 可选。当前元素的索引        |
|                                                  | arr          | 可选。当前元素所属的数组对象    |
| initialValue                                     |              | 可选，传递给函数的初始值      |

### map()和forEach()的区别

定义：

foreEach()方法：针对每一个元素执行提供的函数。

map()方法：创建一个新的数组，其中每一个元素由调用数组中的每一个元素执行提供的函数得来。

区别：forEach()方法不会返回执行结果，而是undefined。也就是说，forEach()会修改原来的数组。而map()方法会得到一个新的数组并返回。

相同点：

1.都是循环遍历数组中的每一项

2.每次执行匿名函数都支持三个参数，参数分别为item（当前每一项），index（索引值），arr（原数组）

3.匿名函数中的this都是指向window

4.只能遍历数组

```javascript
["1", "2", "3"].map(parseInt);  // [1, NaN, NaN]
// parseInt(string, radix) -> map(parseInt(value, index))
/*  first iteration (index is 0): */ parseInt("1", 0); // 1
/* second iteration (index is 1): */ parseInt("2", 1); // NaN
/*  third iteration (index is 2): */ parseInt("3", 2); // NaN
```

### includes()

方法用来判断一个数组是否包含一个指定的值，如果是返回 true，否则false

```
let site = ['runoob', 'google', 'taobao'];
site.includes('runoob'); // true  
site.includes('baidu'); // false
```

### some() 和every()

some() 方法会依次执行数组的每个元素：如果有一个元素满足条件，则表达式返回true , 剩余的元素不会再执行检测。如果没有满足条件的元素，则返回false。注意： some() 不会对空数组进行检测。 some() 不会改变原始数组。

every方法是所有成员的返回值都是true，整个every方法才返回true，否则返回false。

注意，对于空数组，some方法返回false，every方法返回true，回调函数都不会执行。

```js
array.some(function(currentValue, index, arr), thisValue)
```

| 参数                                 | 详细           | 描述                                                                   |
| ---------------------------------- | ------------ | -------------------------------------------------------------------- |
| function(currentValue, index, arr) | currentValue | 必须。当前元素的值                                                            |
| 必须，函数，数组中的每个元素都会执行这个函数             | index        | 可选。当前元素的索引值                                                          |
|                                    | arr          | 当前元素属于的数组对象                                                          |
| thisValue                          | thisValue    | 可选，对象作为该执行回调时使用，传递给函数，用作“this”的值。如果省略了thisValue，“this”的值为“undefined” |

### filter()

filter用于对数组进行过滤，不改变原数组。其中，函数 function 为必须，数组中的每个元素都会执行这个函数。且如果返回值为 true，则该元素被保留；函数的第一个参数 currentValue 也为必须，代表当前元素的值。

返回数组，包含了符合条件的所有元素。如果没有符合条件的元素则返回空数组。

```
Array.filter(function(currentValue, indedx, arr), thisValue)
```

### join

join() 方法用于把数组中的所有元素放入一个字符串。元素是通过指定的分隔符进行分隔的

```js
array.join(seperator);
```

| 参数        | 描述                        |
| --------- | ------------------------- |
| seperator | 可选。指定要使用的分隔符，如果省略，则使用逗号分隔 |

### slice

从已有的数组中返回特定的元素，该方法并不会修改数组，而是返回一个子数组

```js
array.slice(start, end);
```

| 参数    | 描述                                                                                         |
| ----- | ------------------------------------------------------------------------------------------ |
| start | 必须。规定从何处开始选取。如果是负数，那么它规定从数组尾部开始算起的位置。也就是说，-1指最后一个元素，-2指倒数第二个元素，以此类推。                       |
| end   | 可选。规定从何处结束选取。改参数是数组片段结束处的数组下表。如果没有指定，那么切分的数组包含从start到数组结束的所有元素。如果参数为负数，那么规定的是从数组尾部开始算起的元素。 |
|       | 返回一个新的数组，包括从start到end（不包括）的array的元素                                                        |

```javascript
[0, 1, 2, 3, 4].slice(1, 3); // [1, 2]
```

### splice

这种方法会改变原始数组

```js
array.splice(index, howmany, item1,……,itemX);
```

| 参数             | 描述                                                                |
| -------------- | ----------------------------------------------------------------- |
| index          | 必须。规定从何处添加/删除元素。<br />该参数是开始插入（或）删除的数组元素的下标，必须是数字                 |
| howmany        | 必需。规定应该删除多少元素。必须是数字，也可以是“0”<br />如果未规定此参数，则删除从index开始到元素组结尾的所有元素。 |
| item1,……,itemX | 可选，要添加到数组的新元素                                                     |
| 返回             | array，如果从array中有删除的元素，则返回的是含有被删除的元素的数组                            |

```javascript
let arr = [0, 1, 2, 3];
arr.splice(1, 0, 5);
console.log(arr); // [0, 5, 1, 2, 3]
```

### indexOf()和lastIndexOf()

indexOf方法返回给定元素在数组中第一次出现的位置，如果没有出现则返回-1。可以接受第二个参数，表示搜索的开始位置

```js
['a', 'b', 'c'].indexOf('a', 1) // -1
```

lastIndexOf方法返回给定元素在数组中最后一次出现的位置，如果没有出现则返回-1。

### push 和pop

```
var x = array.push('lemon'); // 向数组最后添加一个元素,返回新数组的长度
var y = array.pop(); // 删除数组最后一个元素，返回删除的元素
```

### shift 和unshift

如果插入多个，把多个当成整体插入

```js
var x = array.unshift('lemon'); // 向数组开头添加一个元素,返回新数组的长度
var y = array.shift(); // 删除数组开头一个元素，返回删除的元素
```

### find()和findIndex()

```javascript
array.find(function(currentValue, index, arr),thisValue)
```

find()方法为数组中的每个元素都调用一次函数执行

- 当数组中的元素在测试条件时返回 *true* 时, find() 返回符合条件的元素，之后的值不会再调用执行函数。
- 如果没有符合条件的元素返回 undefined

### concat

连接两个或多个数组，该方法不会改变现有的数组，而仅仅会返回被连接数组的一个副本。

`arrayObject.concat(arrayX,arrayX,......,arrayX)`：必需。该参数可以是具体的值，也可以是数组对象。可以是任意多个。

```javascript
[1, 2].concat(4, 5); // [1, 2, 4, 5]
[1, 2].concat([4], [5]); // [1, 2, 4, 5]
```

### flat

```javascript
var newArray = arr.flat([depth]);
// depth 可选，指定要提取嵌套数组的结构深度，默认值为 1。
// 一个包含将数组与子数组中所有元素的新数组
```

```javascript
var arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat();
// [1, 2, 3, 4, [5, 6]]

var arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2);
// [1, 2, 3, 4, 5, 6]

//使用 Infinity，可展开任意深度的嵌套数组
var arr4 = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]]];
arr.toString().split(',');
arr4.flat(Infinity);
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

### reverse

用于颠倒数组中元素的顺序，返回颠倒顺序后的数组，会改变原数组

```javascript
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.reverse();
// ['Mango', 'Apple', 'Orange', 'Banana']
```

### sort

对数组的元素进行排序，并返回数组。默认排序顺序是在将元素转换为字符串，然后比较它们的 UTF-16 代码单元值序列时构建的

sort 会改变原数组

```javascript
arr.sort([compareFunction])
// 举例 compareFunction
function compare(a, b) {
  if (a < b ) {           // 按某种排序标准进行比较，a 小于 b
    return -1;
  }
  if (a > b ) {
    return 1;
  }
  // a must be equal to b
  return 0;
}
```

如果指明了 `compareFunction` ，那么数组会按照调用该函数的返回值排序。即 a 和 b 是两个将要被比较的元素：

- 如果 `compareFunction(a, b)` 小于 0 ，那么 a 会被排列到 b 之前；

- 如果 `compareFunction(a, b)` 等于 0 ， a 和 b 的相对位置不变

- 如果 `compareFunction(a, b)` 大于 0 ， b 会被排列到 a 之前。

- 如果省略，元素按照转换为的字符串的各个字符的 Unicode 位点进行排序。


### 数组方法效率对比

while > for > for-of > forEach > filter > map > for-in

### 数组空位元素处理

- `for...of`循环会遍历空位

- `forEach()`, `filter()`, `every()` 和`some()`都会跳过空位。
- `map()`会跳过空位，但会保留这个值
- `join()`、`toString()`、`find()`、`findIndex()`会将空位视为`undefined`，而`undefined`和`null`会被处理成空字符串
- ES6 会将空位处理成undefined

```javascript
let arr = ['a',,'b'];
arr.forEach((x,i) => console.log(i)); // 0 2
console.log(arr); // ['a',,'b']
arr.filter(x => true) // ['a','b']
[,,'a'].every(x => x==='a') // true
[,,'a'].some(x => x !== 'a') // false
[,,'a'].map(x => 1) // [,,1]

[,'a',undefined,null].join('#') // "#a##"
[,'a',undefined,null].toString() // ",a,,"

Array.from(['a',,'b']); // [ "a", undefined, "b" ]
[...['a',,'b']]; // [ "a", undefined, "b" ]
[,'a'].find(x => true) // undefined
[,'a'].findIndex(x => true) // 0
```

### 数组去重的方法

1. 利用 Set去重

   ```javascript
   Array.from(new Set(arr));
   [...new Set(arr)];
   ```

2. 嵌套for循环，splice去重

   ```javascript
   function unique(arr){            
     for(var i=0; i<arr.length; i++){
       for(var j=i+1; j<arr.length; j++){
         if(arr[i]==arr[j]){         //第一个等同于第二个，splice方法删除第二个
           arr.splice(j,1);
           j--;
         }
       }
     }
   	return arr;
   }
   ```

3. 利用indexOf去重 或includes

   ```javascript
   function unique(arr) {
       var array = [];
       for (var i = 0; i < arr.length; i++) {
           if (array.indexOf(arr[i]) === -1) {
               array.push(arr[i])
           }
       }
       return array;
   }
   ```

4. 利用sort 去重

   ```javascript
   function unique(arr) {
       arr = arr.sort()
       var arrry= [arr[0]];
       for (var i = 1; i < arr.length; i++) {
           if (arr[i] !== arr[i-1]) {
               arrry.push(arr[i]);
           }
       }
       return arrry;
   }
   ```

5. 利用filter

   ```javascript
   function unique(arr) {
     return arr.filter(function(item, index, arr) {
       //当前元素，在原始数组中的第一个索引==当前索引值，否则返回当前元素
       return arr.indexOf(item, 0) === index;
     });
   }
   ```

6. 利用Map

   ```javascript
   function arrayNonRepeatfy(arr) {
     let map = new Map();
     let array = new Array();  // 数组用于返回结果
     for (let i = 0; i < arr.length; i++) {
       if(map.has(arr[i])) {  // 如果有该key值
         map.set(arr[i], true); 
       } else { 
         map .set(arr[i], false);   // 如果没有该key值
         array .push(arr[i]);
       }
     } 
     return array ;
   }
   ```

7. reduce+includes

   ```javascript
   function unique(arr){
       return arr.reduce((prev,cur) => prev.includes(cur) ? prev : [...prev,cur],[]);
   }
   ```

### 数组扁平化的方法



## String

### replace

replace(regexp/substr, replacement|function)

| 参数          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| regexp/substr | 必须。规定字符串或要替换的模式的RegExp对象。<br />如果该值是一个字符串，仅第一个匹配项会被替换。 |
| replacement   | 必须。用于替换掉第一个参数在原字符串中的匹配部分的字符串     |
| function      | 一个用来创建新子字符串的函数，该函数的返回值将替换掉第一个参数匹配到的结果 |
| 返回string    | 新的字符串，是用 replacement 替换了 regexp 的第一次匹配或所有匹配之后得到的。 |

 replacement 中的 $ 字符具有特定的含义

| 字符             | 替换文本                                            |
| :--------------- | :-------------------------------------------------- |
| $1、$2、...、$99 | 与 regexp 中的第 1 到第 99 个子表达式相匹配的文本。 |
| $&               | 与 regexp 相匹配的子串。                            |
| $`               | 位于匹配子串左侧的文本。                            |
| $'               | 位于匹配子串右侧的文本。                            |
| $$               | 直接量符号。                                        |

```javascript
var str = "hellllo world";
var str1 = str.replace(/(l)l/g, '$1');
console.log(str1); // hello world

var str = "Doe, John";
str.replace(/(\w+)\s*, \s*(\w+)/, "$2 $1");
console.log(str); // John, Doe
```

function时，参数如下：

| 变量名          | 代表的值                                                                                                          |
| ------------ | ------------------------------------------------------------------------------------------------------------- |
| `match`      | 匹配的子串。（对应于上述的$&。）                                                                                             |
| `p1,p2, ...` | 假如replace()方法的第一个参数是一个regExp对象，则代表第n个括号匹配的字符串。例如，如果是用 `/(\a+)(\b+)/` 这个来匹配，`p1` 就是匹配的 `\a+`，`p2` 就是匹配的 `\b+`。 |

```javascript
function render(template, context) {
  return template.replace(/\{\{(.*?)\}\}/g, (match, key) => {
    console.log('match, key', match, key);
    return context[key];
  });
}
const template = "{{name}}很厉name害，才{{age}}岁";
const context = { name: "jawil", age: "15" };
console.log(render(template, context));
// output:
// match, key {{name}} name
// match, key {{age}} age
// jawil很厉name害，才15岁
```

### startsWith() 和endsWith()

用于检测字符串是否以指定的子字符串开始。如果是以指定的子字符串开头返回 true，否则 false。

startsWith() 方法对大小写敏感。

### split

```
stringObject.split(separator, howmany); // 用于把一个字符串分割成字符串数组
```

| 参数        | 描述                                                                                                                                              |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| seperator | 必须。字符串或正则表达式，从该参数指定的地方分隔stringObject                                                                                                            |
| howmany   | 可选。该参数可指定返回的数组的最大长度。如果设置了该参数，返回的子串不会多于这个参数指定的数组。如果没有设置该参数，整个字符串都会被分隔，不考虑它的长度。                                                                   |
| 返回        | 一个字符串数组，该数组是通过在seperator指定的标价出将字符串分割成子串创建的。返回的数组中的子串不包括separator自身。<br />但是，如果seperatoe是包含子表达式的正则表达式，那么返回的数组中包括与这些子表达式匹配的字串（但不包括与整个正则表达式匹配的文本）。 |

### match

在字符串内检索指定的值，或找到一个或多个正则表达式的匹配

```
string.match(regexp);
```

| 参数     | 描述                                                       |
| ------ | -------------------------------------------------------- |
| regexp | 必须。规定要匹配的模式的RegExp对象。如果该参数不是RegExp对象，则需要首先把它传递给RegExp对象  |
| 返回     | array。存放匹配结果的数组。该数组的内容依赖于regexp是否具有全局标志g。如果没找到匹配结果返回null |

```js
// 例子
var str = "The rain in SPAIN stays mainly in the plain";
var n = str.match(/ain/gi);
console.log(n);
// [ain, AIN, ain, ain]
```

### indexOf()

返回某个指定的字符串值在字符串中首次出现的位置

```
stringObject.indexOf(searchvalue, fromindex);
```

| 参数          | 描述                                                                                |
| ----------- | --------------------------------------------------------------------------------- |
| searchvalue | 必须。规定需检索的字符串值                                                                     |
| fromindex   | 可选的整数参数。规定在字符串中开始检索的位置。它的合法取值是 0 到 stringObject.length - 1。如省略该参数，则将从字符串的首字符开始检索。 |

### substr()和substring()

substr() 方法可在字符串中抽取从 start 下标开始的指定数目的字符。

```
// start必需，length可选，不写时到结尾
stringObject.substr(start,length)
```

substring() 方法用于提取字符串中介于两个指定下标之间的字符。

```
// start必需，stop可选，不写时到结尾
stringObject.substring(start,stop)
```

### trim()

trim() 方法用于删除字符串的头尾空白符，空白符包括：空格、制表符 tab、换行符等其他空白符等

### padStart()

用另一个字符串填充当前字符串(如果需要的话，会重复多次)，以便产生的字符串达到给定的长度。从当前字符串的左侧开始填充

```javascript
const str1 = '5';

console.log(str1.padStart(3, '0'));
// expected output: "005"
```

### repeat

字符串重复指定次数

```javascript
const sourceCode = 'repeat for 3 times;'
const repeated = sourceCode.repeat(3) 
console.log(repeated) // repeat for 3 times;repeat for 3 times;repeat for 3 times;
```

### charAt

从一个字符串中返回指定的字符

```javascript
str.charAt(index)
```

index：一个介于 0 和字符串长度减 1 之间的整数。(0~length-1) 如果没有提供索引，charAt() 将使用 0。

## Set

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set

`Set`对象是值的集合，可以按照插入的顺序迭代它的元素。 Set中的元素只会**出现一次**，即 Set 中的元素是唯一的

```js
let mySet = new Set();

mySet.add(1); // Set [ 1 ]
mySet.add("some text"); // Set [ 1, 5, "some text" ]
let o = {a: 1, b: 2};
mySet.add(o);

mySet.add({a: 1, b: 2}); // o 指向的是不同的对象，所以没问题

mySet.has(1); // true

mySet.size; // 5

mySet.delete(5);  // true,  从set中移除5
mySet.has(5);     // false, 5已经被移除

// 数组去重
const numbers = [2,3,4,4,2,3,3,4,4,5,5,6,6,7,5,32,3,4,5]
console.log([...new Set(numbers)]) 
// [2, 3, 4, 5, 6, 7, 32]
```

## Map

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map

```js
let contacts = new Map<K, V>(); // K是键类型，V是值类型
contacts.set('Jessie', {phone: "213-555-1234", address: "123 N 1st Ave"})
contacts.has('Jessie') // true
contacts.get('Hilary') // undefined
contacts.set('Hilary', {phone: "617-555-4321", address: "321 S 2nd St"})
contacts.get('Jessie') // {phone: "213-555-1234", address: "123 N 1st Ave"}
contacts.delete('Raymond') // false
contacts.delete('Jessie') // true
console.log(contacts.size) // 1

const map1 = new Map();
map1.set('0', 'foo');
map1.set(1, 'bar');
map1.values(); // 返回一个新的Iterator对象。它包含按顺序插入Map对象中每个元素的value值
map1.keys(); // 返回一个引用的 Iterator 对象。它包含按照顺序插入 Map 对象中每个元素的key值
for(let items of map1.entries()){
    console.log(items);  // ["foo",1]  ["bar",2]
}
map1.forEach( (value,key,map) => {
     console.log(key,value); // foo 1    bar 2
})
```

map和Object的区别

|          | Map                                                          | Object                                                       |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 意外的键 | Map默认情况不包含任何键，只包含显式插入的键。                | Object 有一个原型, 原型链上的键名有可能和自己在对象上的设置的键名产生冲突。 |
| 键的类型 | Map的键可以是任意值，包括函数、对象或任意基本类型。          | Object 的键必须是 String 或是Symbol。                        |
| 键的顺序 | Map 中的 key 是有序的。因此，当迭代的时候， Map 对象以插入的顺序返回键值。 | Object 的键是无序的                                          |
| Size     | Map 的键值对个数可以轻易地通过size 属性获取                  | Object 的键值对个数只能手动计算                              |
| 迭代     | Map 是 iterable 的，所以可以直接被迭代。                     | 迭代Object需要以某种方式获取它的键然后才能迭代。             |
| 性能     | 在频繁增删键值对的场景下表现更好。                           | 在频繁添加和删除键值对的场景下未作出优化。                   |

### WeakMap

WeakMap的设计目的在于，有时想在某个对象上面存放一些数据，但是这会形成对于这个对象的引用。一旦不再需要这两个对象，就必须手动删除这个引用，否则垃圾回收机制就不会释放对象占用的内存。

而WeakMap的**键名所引用的对象都是弱引用**，即垃圾回收机制不将该引用考虑在内。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。也就是说，一旦不再需要，WeakMap 里面的**键名对象和所对应的键值对会自动消失，不用手动删除引用**。

`WeakMap`与`Map`的区别有两点。

1. `WeakMap`只接受对象作为键名（`null`除外），不接受其他类型的值作为键名。
2. `WeakMap`的键名所指向的对象，不计入垃圾回收机制。一个典型应用场景是，在网页的 DOM 元素上添加数据，使用`WeakMap`结构。当该 DOM 元素被清除，其所对应的`WeakMap`记录就会自动被移除。

```javascript
const wm = new WeakMap();

const element = document.getElementById('example');

wm.set(element, 'some information');
wm.get(element) // "some information"
```

## object

### Object.is

判断两个值是否为同一个值，一般情况下和三等号的判断相同，它处理了一些特殊的情况，比如 -0 和 +0 不再相等，两个 NaN 是相等的

```
Object.is(value1, value2);
```

### Object.keys

返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名，symbol类型的属性不可遍历

```
let obj = { foo : "bar", baz : 18 }

console.log(Object.keys(obj));      //["foo" , "baz"]
```

### Object.values

返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历属性的键值

如果Object.values()方法的参数是一个字符串，会返回各个字符组成的一个数组

### Object.entries

返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值对数组

### Object.assign()

用于将所有可枚举属性的值从一个或多个源对象复制到目标对象，浅拷贝

```js
const target = { a: 1, b:2 };
const source = { b: 4, c: 5 };
const returnedTarget = Object.assign(target, source);
console.log(target);
// { a: 1, b:4, c: 5 }
console.log(returnedTarget);
// { a:1, b: 4, c: 5}
```

扩展运算符和`Object.assign()` ，两者都是浅拷贝。

- Object.assign()方法接收的第一个参数作为目标对象，后面的所有参数作为源对象。然后把所有的源对象合并到目标对象中。它会修改了一个对象，因此会触发 ES6 setter。
- 扩展操作符（…）使用它时，数组或对象中的每一个值都会被拷贝到一个新的数组或对象中。它不复制继承的属性或类的属性，但是它会复制ES6的 symbols 属性。

### Object.freeze()

**冻结**一个对象，使这个对象不能被修改。表现为，不能添加新的属性；不能删除已有属性；不能修改已有属性的值；不能修改该对象已有属性的可枚举性、可配置性、可写性；冻结一个对象后该对象的原型也不能被修改。

### Object.seal()

封闭一个对象，阻止添加新属性并将所有现有属性标记为不可配置。当前属性的值只要原来是可写的就可以改变。

### Object.create(object, [propertiesObject])

创建一个新对象，继承object的属性，可添加propertiesObject添加属性，并对属性作出详细解释(此详细解释类似于defineProperty第二个参数的结构)

```js
var banana = {
    color: 'yellow',
    getColor: function() {
        return this.color
    }
};
var sub_banana = Object.create(banana, {
    // 添加taste属性
    taste: {
        // 详细解释
        writeable: false,
        get: function() {
            console.log('getTaste');
            return 'good';
        }
    },
    weight: {
        value: 600
    }
});
console.log(sub_banana.color);
console.log(sub_banana.getColor);

console.log(sub_banana.taste); // good
console.log(sub_banana.weight); // 600
sub_banana.taste = 'bad'; // 报错，writeable为false不可改变
```

### Object.entries()

返回一个给定对象自身可枚举属性的键值对数组

https://www.jianshu.com/p/6f4537bb89b2

```js
// 示例1 - 传入对象
const obj = { foo: 'bar', baz: 'abc' }；
console.log(Object.entries(obj)); // [['foo', 'bar'], ['bar', 'abc']]

// 示例2.1 - 传入数组
const arr = [1, 2, 3];
console.log(Object.entries(arr)); // [['0', '1'], ['1', '2'], ['2', '3']]

// 示例2.2 - 数组（数组中包含对象）
const arr1 = [{ a: 1 }, 2, 3];
console.log(Object.entries(arr1)); // [['0', { a: 1 }], ['1', '2'], ['2', '3']]
```

### Object.defineProperty()

定义新属性或修改原有的属性

```
Object.defineProperty(obj, prop, descriptor)
```

| 参数         |              | 描述                              |
| ---------- | ------------ | ------------------------------- |
| obj        |              | 要修改的对象                          |
| prop       |              | 要修改的对象的属性                       |
| descriptor | value        | 设置属性的值，默认为undefined             |
|            | writable     | 值是否可以重写，默认为false                |
|            | enumerable   | 目标属性是否可以被枚举，即是否可以被遍历，默认为false   |
|            | configurable | 目标属性是否可以被删除或是否可以再次修改特性，默认为false |

### 创建对象的3种方式

```js
// 1. 字面量
var obj = {
    name: 'Belle',
    age: '22',
};
// 2. new Object
var obj = new Object();
obj.name = 'Belle';
obj.age = '22';
// 3. 构造函数， 构造函数首字母要大写; 不需要return; 必须用new; 调用一次就创建一个对象; 必须用this
function Star(uname, age) {
    this.name = uname;
    this.age = age;
}
var ldh = new Star('Belle', '22')
```

new关键字执行过程：

1. new构造函数在堆中创建了一个空对象
2. this就会指向刚才创建的空对象
3. 执行构造函数里面的代码，给这个空对象添加属性
4. 返回这个对象

```js
// 遍历对象用for in
for (var k in obj) {
    console.log(k); // k 变量输出打印出属性名
    console.log(obj[k]); // obj[k]得到的是属性值
}
```

### 使用变量当做对象的key

```js
const BTN_CODE_ATTR = 'data-btn-code'; //  按钮元素的btnCode属性
const object1 = {
    [BTN_CODE_ATTR]: child.props.btnCode,
    key: vaildChildren.length,
}
```

### 操作符

Null判断运算符??：它的行为类似`||`，但是只有运算符左侧的值为`null`或`undefined`时，才会返回右侧的值

```js
const headerText = response.settings.headerText ?? 'Hello, world!';
const animationDuration = response.settings.animationDuration ?? 300;
const showSplashScreen = response.settings.showSplashScreen ?? true;
```

链判断运算符?. ：在链式调用的时候判断，左侧的对象是否为`null`或`undefined`。如果是的，就不再往下运算，而是返回`undefined`

```
obj?.prop // 对象属性
obj?.[expr] // 同上
func?.(...args) // 函数或对象方法的调用
```

### 其他

1、对象作为键，自动转换为字符串。当对象自动字符串化时，变成了'[Object object]'

```javascript
const a = {};
const b = { key: 'b' };
const c = { key: 'c' };

a[b] = 123;
a[c] = 456;
console.log(a[b]); //456
```

## Math对象

非构造函数，可以直接使用

| 属性 / 方法                 | 作用                     |
| ----------------------- | ---------------------- |
| Math.PI                 | 圆周率                    |
| Math.floor()            | 向下取整                   |
| Math.ceil()             | 向上取整                   |
| Math.round()            | 四舍五入版就近取整，注意-3.5 ，结果-3 |
| Math.abs()              | 绝对值                    |
| Math.max() / Math.min() | 求最大和最小值                |
| Math.random()           | 返回随机浮点数 [0, 1)         |

## Date对象

https://www.imooc.com/article/details/id/23107

Date.prototype.valueOf()

- 语法: dataObj.valueOf()

```javascript
new Date()
// 产生Date对象
// Wed Aug 10 2022 17:57:59 GMT+0800 (中国标准时间)
Date()
// 产生字符串
'Wed Aug 10 2022 17:58:04 GMT+0800 (中国标准时间)'
Date.now() // 1660203307319
```

## 异步方式

ES6规范，是JS中进行异步编程的新解决方案，旧方案是单纯使用回调函数。从语法上来说，Promise是一个构造函数，从功能上来说，Promise对象用来封装一个异步操作并可以获取其成功/失败的结果值。

 Promise优点：指定回调函数的方式更加灵活；支持链式调用，可以解决回调地狱问题。

### promise

promise构造函数：Promise(executor) {}

注：executor会在Promise内部立即同步调用，异步操作在执行器中执行

```js
// example
let promise = new Promise((resolve, reject) => {
    // 同步调用的
    console.log('111');
});
console.log('222');

// expect output: 111 222
```

```jsx
const promise = new Promise((resolve, reject) => {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(reason);
  }
});
promise.then((value) => {
  // success
}, (reason) => {
  // failure
});
```

- resolve作用是，将Promise对象的状态从“未完成”变为“成功”（即从pending变为resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；reject作用是，将Promise对象的状态从“未完成”变为“失败”（即从pending变为rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

- Promise对象 { PromiseState, PromiseResult }
  
  - PromiseState：
    
    - pending：待定，初始状态
    
    - fulfilled：实现，操作成功
    
    - rejected：被否决，操作失败
  
  - Promise状态的变化有3种方法：
    
    - resolve(value) : pending变为fulfilled
    - reject(reason): pending变为rejected
    - 抛出异常：pending变为rejected
  
  - PromiseResult：value 或reason

中止promise联调的方法：有且仅有一个方式：返回pending状态的promise对象

```
return new Promise(() => {})
```

promise的缺点：

- 无法取消Promise，一旦新建它就会立即执行，无法中途取消。
- 如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
- 当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

#### Promise.prototype.then(onResolved, onRejected)

`.then` 或`.catch` 的参数期望是函数，传入非函数则会发生**值透传**。

指定用于得到成功value的成功回调和用于得到失败reason的失败回调，返回一个新的Promise对象

then方法返回是一个promise对象，其结果状态由then指定的回调函数执行的结果决定

1、如果抛出异常, 新 promise 变为 rejected, reason 为抛出的异常

2、如果返回的是非 promise 的任意值, 新 promise 变为 fulfilled, value 为返回的值

3、如果返回的是另一个新 promise, 此 promise 的结果就会成为新 promise 的结果

#### Promise.prototype.catch(onRejected)

指定发生错误时的回调函数。

如果异步操作抛出错误，状态就会变为`rejected`，就会调用`catch()`方法指定的回调函数，处理这个错误。另外，`then()`方法指定的回调函数，如果运行中抛出错误，也会被`catch()`方法捕获。

#### Promise.prototype.finally()

- `.finally()`方法不管Promise对象最后的状态如何都会执行

- `.finally()`方法的回调函数不接受任何的参数，也就是说你在`.finally()`函数中是无法知道Promise最终的状态是`resolved`还是`rejected`的

- 它最终返回的默认会是上一次的Promise对象值，不过如果抛出的是一个异常则返回异常的Promise对象。

```js
promise
.then(result => {···})
.catch(reason => {···})
.finally(() => {···});
```

```javascript
Promise.resolve('1')
  .then(res => {
    console.log(res)
  })
  .finally(() => {
    console.log('finally')
  })
Promise.resolve('2')
  .finally(() => {
    console.log('finally2')
  	return '我是finally2返回的值'
  })
  .then(res => {
    console.log('finally2后面的then函数', res)
  })
```

输出：

```
1
finally2
finally
finally2后面的then函数 2
```

#### Promise.resolve(value)

入参value: 成功的数据或 promise 对象
返回：一个成功/失败的 promise 对象，
作用：快速封装一个Promise对象，传入参数类型不同，则返回结果不同：

- 参数非Promise对象，返回结果为成功Promise对象
- 参数为Promise对象，则参数的结果决定了resolve的结果

```js
let p1 = Promise.resolve(521); // fulfilled 521
let p2 = Promise.resolve(new Promise(resolve, reject) => {});
```

#### Promise.reject(reason)

入参reason: 失败的原因
返回：无论传入什么类型数值，返回结果都是一个失败的Promise{ rejected, 入参}

```js
let p1 = Promise.reject(521); // rejected 521
let p2 = Promise.reject(new Promise(resolve, reject) => {resolve(521)}); // rejected Promise
```

#### Promise.all (promises)

入参promises: 包含 n 个 promise 的数组
返回：一个新的 promise, 只有所有的 promise 都成功才成功, 只要有一个失败了就失败。

返回结果：

（1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。

（2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数，所有的Promise都会被执行

```js
let p1 = Promise.resolve(
    new Promise((resolve, reject) => {
        resolve(1);
    })
);
let p2 = Promise.resolve(
    new Promise((resolve, reject) => {
        resolve(2);
    })
);
let p3 = Promise.resolve(
    new Promise((resolve, reject) => {
        resolve(3);
    })
);
const result = Promise.all([p1, p2, p3]);
console.log(result);
// 全成功时 返回Promise对象{PromiseState: fulfilled, PromiseResult: [1, 2, 3] }
```

#### Promise.race(promises)

入参promises: 包含 n 个 promise 的数组
返回：一个新的 promise, 第一个完成的 promise 的结果状态就是最终的结果状态

#### Promise.allSettled(promises)

入参promises: 包含 n 个 promise 的数组
返回：该方法返回的新的 Promise 实例，一旦结束，状态总是fulfilled，不会变成rejected。状态变成fulfilled后，Promise 的监听函数接收到的参数是一个数组。

只有等到所有这些参数实例都返回结果，不管是fulfilled还是rejected，包装实例才会结束

```js
const resolved = Promise.resolve(42);
const rejected = Promise.reject(-1);

const allSettledPromise = Promise.allSettled([resolved, rejected]);

allSettledPromise.then(function (results) {
  console.log(results);
});
// [
//    { status: 'fulfilled', value: 42 },
//    { status: 'rejected', reason: -1 }
// ]
```

#### Promise.any(promises)

入参promises: 包含 n 个 promise 的数组

返回结果：必须等到所有参数 Promise 变成`rejected`状态才会结束。

（1）p1、p2、p3任意一个变成fulfilled，p的状态就会变成fulfilled，返回这个的结果

（2）p1、p2、p3全部被rejected，p的状态就变成rejected，抛出错误

### async await

async 函数：`async`函数返回的 Promise 对象，必须等到内部所有`await`命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到`return`语句或者抛错。只有`async`函数内部的异步操作执行完，才会执行`then`方法指定的回调函数

1. 函数的返回值为 promise 对象

2. promise 对象的结果由 async 函数执行的返回值决定
   
   - 返回值非promise对象，则promise 对象的结果 fulfilled，result就是返回的值
   - 返回值为promise对象，则promise 对象的结果由该promise对象决定
   - 抛出异常，则promise 对象的结果 rejected，result就是返回的值

3. 当 async 函数被执行时，将立即返回 pending 状态的Promise，是同步的。返回await时，是pending状态的promise

```javascript
async function getData() {
  // 1. return 123
  // 2. return await 123;
  // 3. return Promiese.resolve('I made it');
}
const data = getData();
console.log(data);
data.then(v => console.log(v));

// 1. Promise { 123 }   123
// 2. Promise { <pending> }  123
// 3. Promise { <pending> }  123
```

await 表达式：

1. await 右侧的表达式一般为 promise 对象, 但也可以是其它的值
2. 如果表达式是 promise 对象, await 返回的是 promise 成功的值
3. 如果表达式是其它值, 直接将此值作为 await 的返回值

注意：

1. await 必须写在 async 函数中, 但 async 函数中可以没 有 await
2. 如果 await 的 promise 失败了, 就会抛出异常, 需要通过 try...catch 捕获处理  

### 定时器

setTimeout、setInterval、requestAnimationFrame

有循环定时器的需求，建议使用 `requestAnimationFrame` 来实现。

## AJAX

Asynchronous JavaScript And XML，异步的JS 和XML。通过Ajax可以在浏览器中向服务器发送异步请求，最大的优势是，无刷新获取数据。不是新的编程语言，而是现有编程语言的组合。

优点：可以无需刷新页面而与服务器端进行通信；允许根据用户事件来更新部分页面内容

缺点：没有浏览历史，不能回退；存在跨域问题(同源)；SEO不友好

nodemon: 可以避免来回重新启动server

```
npm install -g nodemon
// 用法
nodemon server.js
```

### 原生Ajax

```js
// 1. 创建对象
const xhr = new XMLHttpRequest();
// 2. 
xhr.open('GET', 'http://127.0.0.1:8000/delay ');
// 3. 发送，可以携带任意参数
xhr.send();
// 4. 暂停
xhr.abort();
// 5. 设置请求头 Content-Type ，设置请求体类型
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.setRequestHeader('name', 'Belle'); // 自定义请求头
// 6. 自动转换 设置响应体数据的类型
xhr.responseType = 'json'; 
// 7. 超时设置2s
xhr.timeout = 2000;
// 8. 超时回调
xhr.ontimeout = function() {
    alert('网络异常，请稍后重试!!');
}
// 9. 网络异常回调
xhr.onerror = function() {
    alert('你的网络似乎出了一些问题！');
}
// 5. 事件绑定，处理服务端返回的结果 readystate 是xhr对象中的属性，状态有 
// 0：未初始化 1：open方法调用完毕 2：send方法调用完毕 3：服务端返回了部分结果 4：服务端返回了全部结果
xhr.onreadystatechange = function() {
    if (xhr.readyState === 4) {
        // 判断响应状态码：200 404 403 401 500
        if (xhr.status >= 200 && xhr.status < 300) {
            // 处理结果 行 头 空行 体
            // 状态码：xhr.status 状态字符串：xhr.statusText 
            // 所有响应头：xhr.getAllResponseHeaders() 响应体：xhr.response
            result.innerHTML = xhr.response;
        }
    }
}
```

### jQuery ajax

```js
$('button').eq(0).click(function() {
      $.get('http://127.0.0.1:8000/jquery-server', { a: 200, b: 200 }, function(data) {
        console.log(data);
      }, 'json');
    });
$('button').eq(1).click(function() {
    $.post('http://127.0.0.1:8000/jquery-server', { a: 200, b: 200 }, function(data) {
        console.log(data);
    });
});
$('button').eq(2).click(function() {
    $.ajax({
        url: 'http://127.0.0.1:8000/jquery-server',
        // 参数
        data: { a: 100, b: 200 },
        // 请求类型
        type: 'GET',
        // 响应体结果
        dataType: 'json',
        // 成功的回调
        success: function(data) {
            console.log(data);
        },
        // 超时时间
        timeout: 2000,
        // 失败的回调
        error: function() {
            console.log('出错啦！');
        },
        // 头信息
        headers: {
            c: 300,
            d: 400,
        },
    });
});
```

### fetch

https://developer.mozilla.org/zh-CN/docs/Web/API/WindowOrWorkerGlobalScope/fetch

https://github.github.io/fetch/

https://segmentfault.com/a/1190000003810652

是在ES6出现的，使用了ES6中的promise对象。fetch不是ajax的进一步封装，而是原生js，没有使用XMLHttpRequest对象。

```js
btn.onclick = function() {
    fetch('http://127.0.0.1:8000/fetch-server', {
        method: 'POST',
        // 请求头
        headers: {
            name: 'Belle',
        },
        // 请求体
        body: 'username=admin&password=admin',
    }).then(response => {
        // response本身并没有返回数据，必须调用.json()方法
        return response.json();
    }).then(response => {
        // response.json()是promise实例对象，通过链式then获取真正的返回值
        console.log('response', response);
    });
}
```

fetch的优点：

- 语法简洁，更加语义化
- 基于标准 Promise 实现，支持 async/await
- 更加底层，提供的API丰富（request, response）
- 脱离了XHR，是ES规范里新的实现方式

fetch的缺点：

- fetch只对网络请求报错，对400，500都当做成功的请求，服务器返回 400，500 错误码时并不会 reject，只有网络错误这些导致请求不能完成时，fetch 才会被 reject。
- fetch默认不会带cookie，需要添加配置项： fetch(url, {credentials: 'include'})
- fetch不支持abort，不支持超时控制，使用setTimeout及Promise.reject的实现的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费
- fetch没有办法原生监测请求的进度，而XHR可以

**Request()**：fetch用到

```javascript
const myRequest = new Request('http://localhost/api', {method: 'POST', body: '{"foo":"bar"}'});

const myURL = myRequest.url; // http://localhost/api
const myMethod = myRequest.method; // POST
const myCred = myRequest.credentials; // omit
const bodyUsed = myRequest.bodyUsed;

fetch(myRequest)
  .then(response => {
    if (response.status === 200) {
      return response.json();
    } else {
      throw new Error('Something went wrong on api server!');
    }
  })
  .then(response => {
    console.debug(response);
    // ...
  }).catch(error => {
    console.error(error);
  });
```

### axios

https://github.com/axios/axios

目前前端最热门的的ajax工具包

```js
axios({
    method: "POST",
    url: "http://localhost:3000/posts",
    // 请求体
    data: {
        title: "Good Weather",
        author: "Whale",
    },
}).then((response) => {
    console.log(response);
    });
};

axios.request({
    method: "GET",
    url: "http://localhost:3000/comments",
}).then((response) => {
    console.log(response);
    });
};
```

请求对象config：（常用）

```
{
     url: '/user',
     method: 'get', // default
     baseURL: 'https://some-domain.com/api/',
     // 对请求数据进行处理
     transformRequest: [function (data, headers) {
        // Do something
        return data;
      }],
      // 对响应数据进行处理
     transformResponse: [function (data) {
        // Do something
        return data;
      }],
     // custom headers to be sent
       headers: {'X-Requested-With': 'XMLHttpRequest'},
       // the URL parameters to be sent with the request
     params: { ID: 12345 },
     data: 'Country=Brasil&City=Belo Horizonte',
     data: { firstName: 'Fred' },
     timeout: 1000, // default is `0` (no timeout)
     // 跨域请求时是否携带cookie，false为不携带
     withCredentials: false, // default
     // indicates the type of data that the server will respond with
       // options: 'arraybuffer', 'document', 'json', 'text', 'stream'
      //  browser only: 'blob'
     responseType: 'json', // default
}
```

创建实例对象：

```js
const joke = axios.create({
    baseURL: 'https://api.apiopen.top',
    timeout: 2000,
});
joke({ url: '/getJoke' }).then(response => {
    console.log(response);
});
joke.get('/getJoke').then();
```

拦截器：

```js
// Add a request interceptor
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// Add a response interceptor
axios.interceptors.response.use(function (response) {
    // Any status code that lie within the range of 2xx cause this function to trigger
    // Do something with response data
    return response;
  }, function (error) {
    // Any status codes that falls outside the range of 2xx cause this function to trigger
    // Do something with response error
    return Promise.reject(error);
  });
```

取消请求：

```js
const CancelToken = axios.CancelToken;
let cancel;

axios.get('/user/12345', {
  cancelToken: new CancelToken(function executor(c) {
    // An executor function receives a cancel function as a parameter
    cancel = c;
  })
});

// cancel the request
cancel();
```

### 解决跨域

https://juejin.cn/post/7112279024210018334

https://juejin.cn/post/6913541999735881741

https://juejin.cn/post/6844903767226351623#heading-15

**同源策略(same-origin-policy)**

解释：是浏览器的一种安全策略，违背同源策略就是跨域。同源：协议、域名、端口号 必须完全相同，浏览器默认两个相同的源之间是可以相互访问资源和操作 DOM 的。css文件的加载不受跨域限制；window.onerror方法默认情况下无法获取跨域脚本的报错详情

作用：保护浏览器的数据安全和用户的隐私安全

限制：

- Cookie、LocalStorage 和 IndexDB 无法读取

- DOM和JS对象无法获得

- AJAX 请求不能发送，被浏览器拦截了 但是有三个标签是允许跨域加载资源：`<script src=XXX>` `<img src=XXX>` `<link href=XXX>`

**跨域**

违反同源策略就是跨域，指浏览器不能执行其他网站的脚本。某些情况下，可以通过各种方式，避开浏览器的安全限制。如果是协议和端口造成的跨域问题“前台”是无能为力的。

**1、JSONP**

原理：`JSONP`通过同源策略涉及不到的"漏洞"，也就是像`img`中的`src`，`link`标签的`href`，`script`的`src`都不受同源策略的限制。利用这个特性，服务端不再返回 JSON 格式的数据，而是返回一段调用某个函数的 js 代码，在 src 中进行了调用，这样实现了跨域。

缺点：只支持 get，因为 script 标签只能使用 get 请求； JSONP 需要后端配合返回指定格式的数据。

```javascript
// 检测用户名是否存在
app.all('/check-username', (request, response) => {
  const data = {
    exist: 1,
    msg: '用户名存在',
  };
  let str = JSON.stringify(data);
  // 返回结果
  // JSONP原理，返回函数调用
  response.end(`handle(${str})`);
});
```

**2、CORS**

CORS：W3C标准，”跨域资源共享”（Cross-origin resource sharing），允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。

原理：浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信。服务器设置`Header[Access-Control-Allow-Origin]`HTTP响应头之后，浏览器将会允许跨域请求。不需要在客户端做任何特殊的操作，完全在服务器中进行处理。

浏览器将`CORS`请求分成两类：简单请求和非简单请求

(1)简单请求

满足以下条件，就是简单请求：

1. 请求方法是：`HEAD`、`POST`、`GET`
2. 请求头只有：`Accept`、`AcceptLanguage`、`ContentType`、`ContentLanguage`、`Last-Event-Id`

简单请求时，浏览器会直接发送跨域请求，并在请求头中携带`Origin`并赋值为当前域名，表明这是一个跨域的请求。

服务器端接到请求后，会根据自己的跨域规则，来返回验证结果。如果验证成功，则会直接返回访问的资源内容。

如果Origin指定的源，不在服务端设置的对应响应头的许可范围内，一般服务器会返回一个403 Forbidden的HTTP响应和控制台错误。

注：后端需要设置的响应头：

| CORS Header属性                          | 解释                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| Access-Control-Allow-Origin （必须）     | 表示接受哪些域名的请求(`*`为所有)                            |
| Access-Control-Allow-Methods             | 设置允许跨域请求的方法                                       |
| Access-Control-Max-Age                   | 设置在86400秒不需要再发送预校验请求                          |
| Access-Control-Expose-Headers（可选）    | 允许跨域请求包含（XMLHttpRequest只能拿到六个字段：`Cache-Control`、`Content-Language`、`Content-Type`、`Last-Modified`、`Expires`、`Pragma` , 如果想拿到其他的需要使用该字段指定 |
| Access-Control-Allow-Credentials（可选） | 设置是否允许传Cookie （要是想传cookie，前端需要设置`xhr.withCredentials = true`，后端设置`Access-Control-Allow-Credentials: true`） |

- Access-Control-Expose-Headers的六个字段属性：
  - ① Cache-Control：通过指定首部字段 `Cache-Control` 的指令，来进行缓存操作的工作机制，多个参数之间可以使用“,”分隔
  - ② Content-Language：会告知客户端，实体主体使用的自然语言（指中文或英文等语言）；
  - ③ Content-Type：说明实体主体内对象的媒体类型
  - ④ Last-Modified：指明资源最终修改时间
  - ⑤Expires：会将资源失效日期告知客户端
  - ⑥Pragma：是HTTP/1.1 之前版本保留的历史遗留字段，仅作为与HTTP/1.0 的向后兼容而定义。

![简单请求](https://images-sally.oss-cn-beijing.aliyuncs.com/img/cors%E7%AE%80%E5%8D%95%E8%AF%B7%E6%B1%82%E7%A4%BA%E4%BE%8B.png)

(2)非简单请求

对服务器有特殊要求的请求，比如请求方法是`PUT`或`DELETE`，或者`Content-Type`字段的类型是`application/json`

1）非简单请求的`CORS`请求会在正式通信之前进行一次预检请求。预检请求格式：

- `OPTIONS`：请求行 的请求方法为`OPTIONS`（专门用来询问的）
- `Origin`：通过预检之后的请求,会自动带上Origin字段
- `Access-Control-Request-Method`：请求的方式
- `Access-Control-Request-Header`

```javascript
OPTIONS /cors HTTP/1.1  //`"预检"`使用的请求方法是 `OPTIONS` , 表示这个请求使用来询问的
Origin: localhost:2333 
Access-Control-Request-Method: PUT // 表示使用的什么HTTP请求方法 
Access-Control-Request-Headers: X-Custom-Header // 表示浏览器发送的自定义字段 
Host: localhost:2332 
Accept-Language: zh-CN,zh;q=0.9 
Connection: keep-alive 
User-Agent: Mozilla/5.0...
```

2）服务器收到预检请求以后，检查了`Origin`、`Access-Control-Request-Method`和`Access-Control-Request-Headers`字段以后，确认允许跨源请求，就可以做出回应。

预检的响应头:

```javascript
HTTP/1.1 200 OK 
Date: Mon, 01 Dec 2008 01:15:39 GMT 
Server: Apache/2.0.61 (Unix) 
Access-Control-Allow-Origin: http://localhost:2332 // 表示http://localhost:2332可以访问数据 
Access-Control-Allow-Methods: GET, POST, PUT 
Access-Control-Allow-Headers: X-Custom-Header 
Content-Type: text/html; charset=utf-8 
Content-Encoding: gzip Content-Length: 0 
Keep-Alive: timeout=2, max=100 
Connection: Keep-Alive 
Content-Type: text/plain
```

3）通过预检后，才会进行正式的请求，浏览器接下来的每次请求就类似于简单请求了

- 如果预检请求验证失败，则会返回403状态，浏览器不会发送真正的跨域请求。

- 通过了预检请求后,请求的时候就会跟简单请求一样,会有一个`Origin`头信息字段。
- 通过预检之后的,浏览器发出正式请求

```javascript
PUT /cors HTTP/1.1 
Origin: http://api.bob.com // 通过预检之后的请求,会自动带上Origin字段 
Host: api.alice.com X-Custom-Header: value 
Accept-Language: en-US 
Connection: keep-alive 
User-Agent: Mozilla/5.0...
```

3、proxy代理

此处介绍的使用devServer解决跨域，其实原理就是http proxy

将所有ajax请求发送给devServer服务器，再由devServer服务器做一次转发，发送给数据接口服务器

由于ajax请求是发送给devServer服务器的，不存在跨域。

注：所谓跨域，是指浏览器向客户端发请求造成的，而devServer由于是用node平台发送的http请求，自然也不涉及到跨域问题。

webpack-dev-server解决办法，在devServer配置

```javascript
devServer: {
    proxy: {
      // 当前端请求/api地址时，会将请求转发到http://localhost:8000
      "/api": {
        target: "http://localhost:8000",
        pathRewrite: {"^/api" : ""}
      }
    },
  },
```

```java
// 使用时
axios.get('/api/cors-server').then(result => console.log(result));
```

4、window.postMessage(message, targetOrigin, [transfer])

5、websocket 是一种双向通信协议，在建立连接之后，WebSocket 的 server 与 client 都能主动向对方发送或接收数据

## 装饰器

https://efe.baidu.com/blog/introduction-to-es-decorator/

装饰器有4种：

- 放在`class`上的“类装饰器”。
- 放在属性上的“属性装饰器”，这需要配合另一个Stage 0的[类属性语法](https://gist.github.com/jeffmo/054df782c05639da2adb)提案，或者只能放在对象字面量上了。
- 放在方法上的“方法装饰器”。
- 放在`getter`或`setter`上的“访问器装饰器”。

装饰器执行时间：

- 装饰器是在声明期就起效的，并不需要类进行实例化。类实例化并不会致使装饰器多次执行，因此不会对实例化带来额外的开销。
- 按编码时的声明顺序执行，并不会将属性、方法、访问器进行重排序。

```javascript
function log(message) {
    return function() {
        console.log(message);
    }
}

console.log('before class');

@log('class Bar')
class Bar {
    @log('class method bar');
    bar() {}

    @log('class getter alice');
    get alice() {}

    @log('class property bob');
    bob = 1;
}

console.log('after class');

let bar = {
    @log('object method bar')
    bar() {}
};

// output
// before class
// class method bar
// class getter alice
// class property bob
// class Bar
// after class
// object method bar
```

### Object.defineProperty

在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象

```javascript
Object.defineProperty(obj, prop, descriptor)
```

- `obj`：要在其上定义属性的对象。
- `prop`：要定义或修改的属性的名称。
- `descriptor`：将被定义或修改的属性描述符
- 返回值：obj

属性描述符：

`configurable`、`enumerable`、`writable`是必然存在的，随后根据放在属性、方法上还是放在访问器上决定是`value`还是`get/set`。

当装饰器作用在类属性时，没有`value`和`get`或`set`，多出一个`initializer`属性。

| 键值           | 功能                                                                                                                                                                               | 默认        |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| configurable | 当且仅当该属性的 `configurable` 键值为 `true` 时，该属性的描述符才能够被改变，同时该属性也能从对应的对象上被删除。如果设为`false`，将阻止某些操作改写该属性，比如无法删除该属性，也不得改变该属性的属性描述对象（`value`**属性除外**）。也就是说， configurable 属性控制了属**性描述对象的可写性**。 | false     |
| enumerable   | 当且仅当该属性的 `enumerable` 键值为 `true` 时，该属性才会出现在对象的枚举属性中。                                                                                                                             | false     |
| writable     | 当且仅当该属性的 `writable` 键值为 `true` 时，属性的值，也就是上面的 `value`，才能被改变。                                                                                                                      | false     |
| value        | 该属性对应的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。                                                                                                                                        | undefined |
| get          | 属性的 getter 函数，如果没有 getter，则为 `undefined`。当访问该属性时，会调用此函数。执行时不传入任何参数，但是会传入 `this` 对象（由于继承关系，这里的`this`并不一定是定义该属性的对象）。该函数的返回值会被用作属性的值。                                               | undefined |
| set          | 属性的 setter 函数，如果没有 setter，则为 `undefined`。当属性值被修改时，会调用此函数。该方法接受一个参数（也就是被赋予的新值），会传入赋值时的 `this` 对象。                                                                                 | undefined |
| initializer  | 在类构造函数执行时，`initializer`返回的值作为属性的值。                                                                                                                                               |           |

### 类装饰器

装饰器在编译阶段就执行了

装饰器的执行顺序为：首先执行属性装饰器，然后执行方法装饰器，其次是方法参数装饰器，最后是类装饰器。

类装饰器的参数 是类本身，接收类 本身一个

1、不带参数的类装饰器，添加属性

```javascript
@testable
class MyTestableClass {
  // ...
}
function testable(target) {
  target.isTestable = true;
}
MyTestableClass.isTestable // true
```

2、带参数的类装饰器，添加属性

```javascript
function testable(isTestable) {
  return function(target) {
    target.isTestable = isTestable;
  }
}
@testable(true)
class MyTestableClass {}
MyTestableClass.isTestable // true

@testable(false)
class MyClass {}
MyClass.isTestable // false
```

3、添加实例属性，可以通过目标类的 prototype 对象操作。

```javascript
export function mixins(...list) {
  return function (target) {
    Object.assign(target.prototype, ...list)
  }
}

// main.js
import { mixins } from './mixins'
const Foo = {
  foo() { console.log('foo') }
};
@mixins(Foo)
class MyClass {}
let obj = new MyClass();
obj.foo() // 'foo'
```

### 类方法装饰器

装饰器可以修饰类的方法，接收3个参数：target, name, descriptor

- `target`：类的原型对象，类名.prototype。装饰器的本意是要“装饰”类的实例，但是这个时候实例还没生成，所以只能去装饰原型

- `name`：所要装饰的属性名

- `descriptor`：该属性的描述对象（最常用）

```javascript
lass Math {
  @log
  add(a, b) {
    return a + b;
  }
}
function log(target, name, descriptor) {
  var oldValue = descriptor.value;
  descriptor.value = function(...args) {
    console.log(`Calling ${name} with`, arg);
    return oldValue.apply(this, ...args);
  };
  return descriptor;
}
const math = new Math();
math.add(2, 4);
```

### 类属性装饰器

```javascript
function randomize(target, key, descriptor) {
    let raw = descriptor.initializer;
    // initializer返回的值作为属性的值
    descriptor.initializer = function() {
        let value = raw.call(this);
        value += '-' + Math.floor(Math.random() * 1e6);
        return value;
    };
}

class Alice {
    @randomize;
    name = 'alice';
}

console.log((new Alice()).name); // alice-776521
```

## 正则表达式

https://www.cnblogs.com/afarmer/archive/2011/08/29/2158860.html

正则表达式（regular expression）是用于匹配字符串中字符组合的模式，在js中，正则表达式也是对象。

 正则表达式通常用来检索、替换那些复核某个模式或规则的文本

### 创建正则表达式

```js
// 1. 通过RegExp对象的构造函数
var regExp = new RegExp(/123/);
// 2. 通过字面量创建
var regExp = /123/;
```

### 正则方法

测试正则表达式test

```js
// 检测字符串是否符合正则表达式规则，返回true或者false
regexObj.test(str);
```

replace()  字符串替换

```
使用正则表达式且不区分大小写将字符串中的 Microsoft 替换为 Runoob 
var txt = str.replace(/microsoft/i,"Runoob");  // replace() 方法使用正则表达式
var txt = str.replace("Microsoft","Runoob");   // replace() 方法使用字符串
```

search() 方法 。用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串，并返回子串的起始位置。

```
var str = "Visit Runoob!";
var n = str.search(/Runoob/i);  //6
```

exec()方法：用于检索字符串中的正则表达式的匹配。该函数返回一个数组，其中存放匹配的结果。如果未找到匹配，则返回值为 null。

```javascript
var str="Hello world!";
//查找"Hello"
var patt=/Hello/g;
var result=patt.exec(str);
document.write("返回值: " +  result); 
//查找 "RUNOOB"
patt=/RUNOOB/g;
result=patt.exec(str);
document.write("<br>返回值: " +  result);
// Returned value: Hello
// Returned value: null
```

### 常用的特殊字符

- 字符类

| 符号            | 说明                    |
| ------------- | --------------------- |
| ^             | 表示匹配行首的文本             |
| $             | 表示匹配行尾的文本             |
| [abc]         | abc三选一                |
| [a-z]         | 表示a-z的范围，小写           |
| [a-zA-Z0-9_-] | 字符组合，a-z 0-9 _-字符里面取一 |
| [^abc]        | []里面加^表示取反            |

- 量词符：用来设置某个模式出现的次数

| 量词    | 说明                |                       |
| ----- | ----------------- | --------------------- |
| *     | 重复零次或更多次          | /^a*$/                |
| .     | 匹配任何单个字符          | /src\/(.*)\/index/    |
| +     | 重复一次或更多次          | /^a+$/                |
| ？     | 重复零次或一次           | /^a?$/                |
| {n}   | 重复n次              | /^a{3}$/              |
| {n,}  | 重复n次或更多次          |                       |
| {n,m} | 重复n到m次，注意中间不能出现空格 | /^[a-zA-Z0-9]{1,32}$/ |

```javascript
/[defgh]*/.test('abc'); // true
```



- 预定义类

| 预定类 | 说明                                              |
| --- | ----------------------------------------------- |
| \d  | 匹配0-9之间的任一数字，相当于[0-9]                           |
| \D  | 匹配所有0-9以外的字符                                    |
| \w  | 匹配任一的字母、数字、下划线，相当于[A-Za-z0-9_]                  |
| \W  | 除所有字母、数字和下划线以外的字符                               |
| \s  | 匹配空格（包括换行符、制表符、空格符等），相当于[\t\r\n\v\f]            |
| \S  | 匹配非空格的字符，                                       |
| \b  | 单词边界 单词可以是中文字符,英文字符,数字；符号可以是中文符号,英文符号,空格,制表符,换行 |
| \B  | 非单词边界                                           |

- 正则表达式参数

```
/表达式/[switch]
// g-全局匹配 i-忽略大小写 gi-全局匹配+忽略大小写
```

```javascript
"/iframe_godzilla?appId=pks".match(/godzilla.+/i); // ['godzilla?appId=pks']
"/iframe_godzilla?appId=pks".match(/godzilla+/i); // ['godzilla']
```

### 分组

使用括号包裹的就是分组

https://segmentfault.com/a/1190000021043947

https://blog.csdn.net/lihefei_coder/article/details/53022253

捕获性分组()：会把每个分组里匹配的值都保存起来

非捕获分组(?:)：分组`(?:)`会作为匹配校验，并出现在匹配结果字符里面，但不作为子匹配返回

正向前瞻(?=)：表示后面要有什么

反向前瞻(?!)：表示后面不能有什么

正向后顾(?<)：表示前面要有什么

反向后顾(?<!)：表示前面不能有什么

```javascript
// 捕获分组
var str = "Doe, John";
var str2 = str.match(/(\w+)\s*, \s*(\w+)/);
console.log(str2); // ['Doe, John','Doe','John',index: 0,input: 'Doe, John',groups: undefined]

// 非捕获分组
var arr1 = str.match(/(?:[a-z]+)\s(?:[a-z]+)/);
console.log(arr2); //['hello world']

//正向前瞻，匹配.jpg后缀文件名
var str = '123.jpg,456.gif,abc.jpg';
var arr3 = str.match(/\w+(?=\.jpg)/g); //正向前瞻匹配
console.log(arr3); //['123', 'abc']   返回结果正确，没有匹配456.gif

//反向前瞻，匹配3个及以上的a，而且后面不能有000的字符
var str = 'aaa000 aaaa111 aaaaaaa222';
var arr4 = str.match(/a{3,}(?!000)/g); //反向前瞻匹配
console.log(arr4); //['aaaa', 'aaaaaaa']   返回结果正确，没有匹配aaa000
```

### 贪婪匹配和非贪婪匹配

- 贪婪匹配：尽可能匹配最长的字符串
- 非贪婪匹配： 尽可能匹配最短的字符串

默认情况下匹配都是贪婪模式，如果要改成非贪婪模式，只需要量词后面加上一个问号?

```javascript
aa<div>test1</div>bb<div>test2</div>cc
// 贪婪模式的结果为：
<div>test1</div>bb<div>test2</div>
// 非贪婪模式的结果为：
<div>test1</div>
```

## Iterator

一个数据结构只要具有`Symbol(Symbol.iterator)`属性，就可以认为是“可遍历的”。`Symbol(Symbol.iterator)`属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。属性名`Symbol.iterator`，它是一个表达式，返回`Symbol`对象的`iterator`属性，这是一个预定义好的、类型为 Symbol 的特殊值，所以要放在方括号内。

```javascript
console.log(Symbol.iterator)
// Symbol(Symbol.iterator)
```

Iterator 的遍历过程是这样的：

（1）创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。
（2）第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员。
（3）第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员。
（4）不断调用指针对象的`next`方法，直到它指向数据结构的结束位置。

每一次调用`next`方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含`value`和`done`两个属性的对象。其中，`value`属性是当前成员的值，`done`属性是一个布尔值，表示遍历是否结束。

原生具备 Iterator 接口的数据结构：Array、Map、Set、String、TypedArray、函数的 arguments 对象、NodeList 对象

调用Iterator的场合：

1. for...of
2. 在以上类型中进行解构赋值
3. 在以上类型中使用扩展运算符
4. yield\*

使普通对象可遍历：

```javascript
//方法一：
var obj = {
    a:1,
    b:2,
    c:3
};

obj[Symbol.iterator] = function(){
	var keys = Object.keys(this);
	var count = 0;
	return {
		next(){
			if(count<keys.length){
				return {value: obj[keys[count++]],done:false};
			}else{
				return {value:undefined,done:true};
			}
		}
	}
};

for(var k of obj){
	console.log(k);
}


// 方法二
var obj = {
    a:1,
    b:2,
    c:3
};
obj[Symbol.iterator] = function*(){
    var keys = Object.keys(obj);
    for(var k of keys){
        yield [k,obj[k]]
    }
};

for(var [k,v] of obj){
    console.log(k,v);
}

```

## generator

执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

Generator 函数是一个普通函数，但是有两个特征。一是，`function`关键字与函数名之间有一个星号；二是，函数体内部使用`yield`表达式，定义不同的内部状态.

由于 Generator 函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。

遍历器对象的`next`方法的运行逻辑如下。

（1）遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。

（2）下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。

（3）如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。

（4）如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

```javascript
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}
var hw = helloWorldGenerator();
```

由于 Generator 函数就是遍历器生成函数，因此可以把 Generator 赋值给对象的`Symbol.iterator`属性，从而使得该对象具有 Iterator 接口。

```javascript
var myIterable = {};
myIterable[Symbol.iterator] = function* () {
  yield 1;
  yield 2;
  yield 3;
};

[...myIterable] // [1, 2, 3]
```

`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。

```javascript
function* f() {
  for(var i = 0; true; i++) {
    var reset = yield i;
    if(reset) { i = -1; }
  }
}

var g = f();

g.next() // { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next(true) // { value: 0, done: false }
```

上面代码先定义了一个可以无限运行的 Generator 函数`f`，如果`next`方法没有参数，每次运行到`yield`表达式，变量`reset`的值总是`undefined`。当`next`方法带一个参数`true`时，变量`reset`就被重置为这个参数（即`true`），因此`i`会等于`-1`，下一轮循环就会从`-1`开始递增。

## 其他

### setTimeout  和setInteval

(1)setTimeout 只执行 function 一次

设置为0的时候，也会有间隔是4ms

```
var timeout = setTimeout(code, millisec, args, ……);
```

|                        | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| code                   | 必须，要调用的函数后要执行的JavaScript代码串                 |
| millisec               | 必须。在执行代码前需等待的毫秒数                             |
| `arg1, ..., argN` 可选 | 附加参数，一旦定时器到期，它们会作为参数传递给function       |
| 返回                   | 返回值一个正整数，表示定时器的编号。这个值可以传递给clearTimeout()来取消该定时器。 |

（2）setInterval

按照指定的周期（以毫秒计）来调用函数或计算表达式。该方法会不停地调用函数，直到 clearInterval() 被调用或窗口被关闭。

```jsx
let time = setInterval(() => {
    // 可转债且选择证券户，券面金额自动指令申购上限
    if (secuType == DICT.CONVT_secu_type_debtpayable) {
        this.props.form.setFieldsValue({ couponAmt: this.uiState.pageData.extInfo});
        clearInterval(time);
    }
}, 100);
```

### URLSearchParams

**`URLSearchParams`** 接口定义了一些实用的方法来处理 URL 的查询字符串。

```javascript
var paramsString = "?q=URLUtils.searchParams&topic=api"
var searchParams = new URLSearchParams(paramsString); // 返回URLSearchParams对象
console.log(searchParams.toString()); // q=URLUtils.searchParams&topic=api

// 判断是否存在搜索参数
searchParams.has("topic") === true; // true
// 获取指定搜索参数的第一个值
searchParams.get("topic") === "api"; // true
// 获取指定搜索参数的所有值，返回值是一个数组
searchParams.getAll("topic"); // ["api"]
// 插入一个指定的键值对作为新的搜索参数
searchParams.append("topic", "webdev");
searchParams.toString(); // "q=URLUtils.searchParams&topic=api&topic=webdev"
// 设置一个搜索参数的新值，加入原来有多个值将删除其他所有的值
searchParams.set("topic", "More webdev");
searchParams.toString(); // "q=URLUtils.searchParams&topic=More+webdev"
searchParams.delete("topic");
searchParams.toString(); // "q=URLUtils.searchParams"
```

### Proxy

在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

```js
// target参数表示所要拦截的目标对象，handler参数也是一个对象，用来定制拦截行为。
var proxy = new Proxy(target, handler);
```

```typescript
// 实例
/**
 *  框架 window
 *  访问 appWindow 的属性时映射到 proxy 代理的 window
 * */

function getKey(key: string) {
  return `${process.env.APP_ID}_${key}`;
}

export default new Proxy(window, {
  get(target: any, key: string) {
    return Reflect.get(target, getKey(key));
  },
  set(target: any, key: string, value) {
    return Reflect.set(target, getKey(key), value);
  },
});
```

```javascript
const target = {
  notProxied: "original value",
  proxied: "original value"
};

const handler = {
  get: function(target, prop, receiver) {
    if (prop === "proxied") {
      return "replaced value";
    }
    return Reflect.get(...arguments);
  }
};

const proxy = new Proxy(target, handler);

console.log(proxy.notProxied); // "original value"
console.log(proxy.proxied);    // "replaced value"
```

### Reflect.get()

`Reflect.get()`方法与从 对象 (`target[propertyKey]`) 中读取属性类似，但它是通过一个函数执行来操作的。

```javascript
Reflect.get(target, propertyKey, receiver)
// target:需要取值的目标对象
// propertyKey:需要获取的值的键值
// receiver:如果`target`对象中指定了`getter`，`receiver`则为`getter`调用时的`this`值。
```

```javascript
// Object
var obj = { x: 1, y: 2 };
Reflect.get(obj, "x"); // 1

// Array
Reflect.get(["zero", "one"], 1); // "one"

// Proxy with a get handler
var x = {p: 1};
var obj = new Proxy(x, {
  get(t, k, r) { return k + "bar"; }
});
Reflect.get(obj, "foo"); // "foobar"
```

### getter setter

1. getter

   有时需要允许访问返回动态计算值的属性，或者你可能需要反映内部变量的状态，而不需要使用显式方法调用。在JavaScript中，可以使用 *getter* 来实现。可以使用数值和字符串作为标识，必须不带参数。

   ```js
   // 要绑定到给定函数的属性名, 可以使用计算属性名的表达式
   {get prop() { ... } }
   {get [expression]() { ... } }
    // 移除getter
   delete language.current;
   ```

2. setter

   在 javascript 中，如果试着改变一个属性的值，那么对应的 setter 将被执行。setter 经常和 getter 连用以创建一个伪属性

   可以使用数值和字符串作为标识，必须有一个明确的参数。

   prop: 要绑定到给定函数的属性名 ，该属性是伪属性，访问是undefined

   val: 用于保存尝试分配给prop的值的变量的一个别名
   expression: 从 ECMAScript 2015 开始，还可以使用一个计算属性名的表达式

   绑定到给定的函数

   ```js
   // 要绑定到给定函数的属性名, 可以使用计算属性名的表达式
   {set prop(val) { . . . }}
   {set [expression](val) { . . . }}
   // 移除setter
   delete language.current;
   ```

   ```js
   const language = {
     set current(name) {
       this.log.push(name);
     },
     log: []
   };
   
   language.current = 'EN';
   language.current = 'FA';
   
   console.log(language.log);
   console.log(language.current);
   // expected output: Array ["EN", "FA"]
   //  expected output: undefined
   ```

### 位运算符

常见的位运算有以下几种：

| 运算符 | 描述 | 运算规则                                                 |
| ------ | ---- | -------------------------------------------------------- |
| `&`    | 与   | 两个位都为1时，结果才为1                                 |
| ｜     | 或   | 只有有一个为1就是1，都为0时才为0                         |
| `^`    | 异或 | 两个位相同为0，相异为1                                   |
| `~`    | 取反 | 0变1，1变0                                               |
| `<<`   | 左移 | 各二进制位全部左移若干位，高位丢弃，低位补0              |
| `>>`   | 右移 | 各二进制位全部右移若干位，正数左补0，负数左补1，右边丢弃 |

1、取反

在计算机中，正数用原码表示，负数使用补码存储，首先看最高位，最高位1表示负数，0表示正数。此计算机二进制码为负数，最高位为符号位。 当发现按位取反为负数时，就直接取其补码，变为十进制：

- 原码就是符号位加上真值的绝对值, 即用第一位表示符号, 其余位表示值
- 正数的反码是其本身。负数的反码是在其原码的基础上, 符号位不变，其余各个位取反
- 正数的补码就是其本身。负数的补码是在其原码的基础上, 符号位不变, 其余各位取反, 最后+1 (即在反码的基础上+1)

```javascript
~6 = ~0000 0110 = 1111 1001
// 1111 1001 反码 1000 0110
// 补码 1000 0111
// 所以6取反是 -7
```

2、左移、右移

```javascript
a=1010 1110
a = a<<2 // 将a的二进制位左移2位、右补0
a = a>>2 // 将a的二进制位右移2位，左补0
```

### try catch

https://juejin.cn/post/6844904000450478094

```javascript
function test (){
    try {
        return 0
    } catch (e) {
        return 1
    }
}
test() // 0

function test1 (){
    try {
        throw new Error()
        return 0 // 这里不会执行到
    } catch (e) {
        return 1
    }
}
test1() //1

function test3 (){
    try {
        return 0
    } finally {
        return 1
    }
}

test3() // 1

function test2() {
    var obj = {a:1};
    try {
        return obj;
    } finally {
        ++obj.a;
    }
}
test2().a // 2

function test3() {
    var obj = {a:1};
    try {
        return obj.a;
    } finally {
        ++obj.a;
    }
}
test1() // 1
```

### 全局属性和方法

| 属性      | 描述                     |
| :-------- | :----------------------- |
| Infinity  | 代表正的无穷大的数值。   |
| NaN       | 指示某个值是不是数字值。 |
| undefined | 指示未定义的值。         |

| 函数                 | 描述                                               |
| :------------------- | :------------------------------------------------- |
| decodeURI()          | 解码某个编码的 URI。                               |
| decodeURIComponent() | 解码一个编码的 URI 组件。                          |
| encodeURI()          | 把字符串编码为 URI。                               |
| encodeURIComponent() | 把字符串编码为 URI 组件。                          |
| escape()             | 对字符串进行编码。                                 |
| eval()               | 计算 JavaScript 字符串，并把它作为脚本代码来执行。 |
| isFinite()           | 检查某个值是否为有穷大的数。                       |
| isNaN()              | 检查某个值是否是数字。                             |
| Number()             | 把对象的值转换为数字。                             |
| parseFloat()         | 解析一个字符串并返回一个浮点数。                   |
| parseInt()           | 解析一个字符串并返回一个整数。                     |
| String()             | 把对象的值转换为字符串。                           |
| unescape()           | 对由 escape() 编码的字符串进行解码。               |
