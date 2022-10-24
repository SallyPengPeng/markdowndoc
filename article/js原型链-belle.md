学`js`时就对原型、原型对象有些头大。当时以为自己已经完全理解了，所以也没有怎么做笔记，最近又遇到类似的问题，发现自己了解的还是太少了，所以决定好好的整理一下~

## 概念

### prototype

`prototype`只有函数、ES6的类才有，注意箭头函数没有`prototype`属性。

《JavaScript高级程序设计》中描述：

> 每个函数都有一个`prototype`（原型）属性，这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。

### [[prototype]]

每个对象都有`[[prototype]]`（原型对象）属性，指向该实例对象的构造函数的`prototype`。这也是可以通过在构造函数的`prototype`上定义属性和方法，使它的实例对象们共享的原因。

注意：`[[prototype]]`无法直接引用，某些浏览器使用`__proto__`来调用`[[prototype]]`。MDN描述，`__proto__`已经从 Web 标准中删除，尽量不要使用。

### constructor

《JavaScript高级程序设计》中描述：

> 只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个`prototype`属性，这个属性指向函数的原型对象。在默认情况下，所有原型对象都会自动获得一个`constructor`属性，指向`prototype`属性所在函数

constructor的作用通过以下例子说明。

```javascript
let a,b;
(function(){
  function A (arg1,arg2) {
    this.a = 1;
    this.b=2; 
  }

  A.prototype.log = function () {
    console.log(this.a);
  }
  a = new A();
  b = new A();
})()
a.log(); // 1
b.log(); // 1
```

通过以上代码可以得到两个对象a,b，他们同为类A的实例。因为A在闭包里，所以现在我们是不能直接访问A的，那如果我想给类A增加新方法怎么办？

```javascript
a.constructor.prototype.log2 = function () {
  console.log(this.b)
}
a.log2(); // 2
b.log2(); // 2
```

### `[[prototype]]`和`prototype`关系

通过`new`关键字可以实现通过构造函数创建实例对象。

new关键字执行过程

1. 在内存中创建一个空对象，给对象添加属性`[[prototype]]`，指向构造函数的`prototype`
2. this指向刚才创建的对象
3. 执行构造函数里面的代码，给这个对象添加属性和方法
4. 返回这个对象

注意：构造函数没有返回值或返回一个 非对象值时，则将创建的新对象返回；当构造函数返回一个对象时，则返回这个对象

以下例子中，`person1`是`Person`的实例对象，都天生有`name`和`sayName`属性，就是因为person1实例对象中有`[[prototype]]`属性，而该属性指向`Person.prototype`

```javascript
function Person() {}
Person.prototype.name = 'tom';
Person.prototype.sayName = function() {
  alert(this.name)
}
let person1 = new Person();
console.log(person1);
console.log(person1.__proto__ === Person.prototype); // true
```

![image-20210825151629034](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05--prototype-%E5%92%8Cprototype%E5%85%B3%E7%B3%BB-b5339f489eca48785c96e60daed5381d-3a7.png)

## 继承时原型链

### 构造函数继承

```javascript
function SuperType(name) {
  this.name = name;
	this.color = ['red', 'blue'];
}
SuperType.prototype.sayName = function() {
 console.log(this.name);
};
function SubType(name, age) {
  // 继承属性
  SuperType.call(this, name);
  this.age = age;
}
// 继承方法
SubType.prototype = new SuperType();

SubType.prototype.sayAge = function() {
  console.log(this.age);
}

let instance1 = new SubType('Tom', 29);
console.log(instance1);
```

![image-20210825155750799](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E7%BB%A7%E6%89%BF-319a42e06c19373ece5beadd4075686d-b15.png)

注意：instance实例的`[[prototype]]`属性没有`constructor`，是因为SubType构造函数定义时，使用了`SubType.prototype = new SuperType()`，所以覆盖了原有的`prototype`，导致没有`constructor`。

### 类继承

```javascript
class Animal {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  sayHello() {
    console.log("Animal is sparking");
  }
}

class Dog extends Animal {
  constructor(name, age, sex) {
    super(name, age);
    this.sex = sex;
  }
  sayHello() {
    console.log(`${this.name} is sparking`);
  }
}
const dog = new Dog("Tom", 5, 'male');
console.log(dog);
```

![类继承](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-%E7%B1%BB%E7%BB%A7%E6%89%BF-4773b3cd6f123cd49748960ad93e2aeb-de2.png)

注意：

1. 类是构造函数的语法糖，原理同构造函数一致

2. 子类Dog的`prototype`对象中内容包括：

   1. 子类Dog中所有定义的方法

   2. 父类Animal中实例对象内容。因为有继承，相当于先给Dog的`prototype`赋值操作，指向`Animal`的实例对象，类似于执行：

      ```javascript
      Dog.prototype = new Animal();
      ```

3. 子类实例化：执行new Dog步骤：

   1. 内存中新建一个空对象，增加`[[prototype]]`属性，指向Dog类的`prototype`。
   2. 获取父类的自有属性
      1. 子类未指定constructor时，执行父类的构造函数`super.call(this, arguments)`，使用call改变父类this为当前实例对象，从而将父类的自有属性添加到当前实例对象上。（添加age name属性）
      2. 指定constructor时，必须调用super，同1。然后添加子类的自有属性（sex）

## 原型链

![原型链](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-%E5%8E%9F%E5%9E%8B%E9%93%BE-4b7c26229a8f399e84d962307a04c481-a35.png)

对象调用属性(方法)：

1. 首先查看自有属性（**hasOwnProperty**存在）
2. 若自有属性不存在，则去原型链上一级一级查看，存在时直接调用
3. 若直到最后一级，仍然不存在，则报错。