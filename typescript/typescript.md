## 基本类型

- 类型声明：给变量设置类型，使变量只能存储某种类型的值。当为变量赋值时，TS编译器会自动检查值是否符合类型声明，符合则赋值，否则报错。

- 自动类型判断：当变量声明和赋值同时进行时，可省略类型声明，ts编译器会自动判断变量类型。

- 类型：除了js基本类型外，还增加以下类型

  |  类型   |       例子        |              描述              |
  | :-----: | :---------------: | :----------------------------: |
  |   any   |         *         |            任意类型            |
  | unknown |         *         |         类型安全的any          |
  |  void   | 空值（undefined） |     没有值（或undefined）      |
  |  never  |      没有值       |          不能是任何值          |
  |  tuple  |       [4,5]       | 元素，TS新增类型，固定长度数组 |
  |  enum   |    enum{A, B}     |       枚举，TS中新增类型       |

```typescript
// unknown
let notSure: unknown = 4;
notSure = 'hello';
// void 用来
let unusable: void = undefined;
// never 永远不会返回结果
function error(message: string): never {
  throw new Error(message);
}

// 元祖Tuple：固定长度的数组
let x: [string, number] = ['hello', 10];
```

枚举enum

```typescript
// enum枚举，enum会自动将值转为0 1 这种，存储空间更小
enum Color { Red = 1, Green, Blue };
let c: Color = Color.Green;
let d: Color = 4;

const enum E { a, b } // 常量枚举，编译会被去掉
```

类型断言：明确告诉编译器数据类型

绕过类型检查方式：1. 类型断言 2. 将数据赋值给一个变化，再传递给方法，而不是直接将字面量传递给方法

```typescript
let someValue: any = 'this is a string';
let someValue: unknown = "this is a string";
let strLength: number = (<string>someValue).length;
let strLength: number = (someValue as string).length;
```

any 和unknown的区别：

```typescript
let a: any;
a = 'hello';
let b: unknown;
b = 'hello';
let e: string;
e = a; // 不报错
e = b: // 报错，提示unknown类型和string类型不匹配
```

其他：

```typescript
// 类型别名
type myType = 1 | 2 | 3;
let k: myType;
// 联合类型
let c: boolean | string; // c是boolean 或者 string
// 交叉类型
let j: { name: string } & { age: number }; // j必须同时有name和age
```

## 类

### 抽象类

以`abstract`开头的类是抽象类：抽象类和其他类区别不大，只是不能用来创建对象，抽象类就是专门用来被继承的类。

抽象方法：没有方法体，抽象方法只能定义在抽象类中，子类必须对抽象方法进行重写。不同的子类可以重写不同的抽象方法，多态性

```javascript
abstract class Animal {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
  abstract sayHello():void;
}

class Dog extends Animal {
  sex: string;
  constructor(name: string, age: number, sex: string) {
    super(name, age);
    this.sex = sex;
  }
	// 必须重写父类抽象方法sayHello，否则会报错
  sayHello() {
    console.log(`${this.name} is sparking`);
  }
}
```

### 属性修饰符

public：在ts中，成员默认为`public`。

private：该成员不能在声明它的外部访问

protected：只能在类或者子类中访问，不能在类的实例中访问

readonly：只读

static：静态成员，也会被继承

```javascript
class Person {
  private age: number;
  protected name: string;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

class Employee extends Person {
  private department: string;
  constructor(name: string, age: number, department: string) {
    super(name, age);
    this.department = department;
  }

  public getElevatorPitch() {
    // 使用this.age会报错，因为age是private
    // return `${this.name} ${this.department} ${this.age}.`;
    return `${this.name} ${this.department}.`;
  }
}

let howard = new Employee("Howard", 20, "Sales");
console.log(howard.department); // 错误
console.log(howard.name); // 错误
console.log(howard.getElevatorPitch()); 
```

readonly：将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。

```javascript
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // 错误! name 是只读的.
```

## interface

用来定义应该包含哪些属性和方法。接口中的方法都是抽象方法。接口命名，一般以大写字母 I 开头，且 I 后面的首个字母一并大写。

```typescript
interface ISquareConfig {
    color: string; // 必须有的属性
    width?: number; // 可选属性
    readonly x: number; // 只读属性
    getPopupContainer?: () => HTMLElement; // 可选的方法属性，使用ES6箭头函数
  	[propName: string]: any; // 索引签名，任意数量的其他属性
}
```

类型别名和接口的区别：

1. 接口可以重复定义，最后以所有定义的属性联合在一起，同名接口中相同属性必须类型相同，否则报错。type不可重复定义

   ```typescript
   type MyType = {
     name: string;
     age: number;
   };
   type myType = {}; // 重复定义会报错
   
   interface MyInterface = {
     name: string;
   };
   interface MyInterface = {
     age: number;
   }; // 重复定义不会报错
   // 使用MyInterface的必须同时满足多个定义的interface中的条件
   ```

2. 接口创建了一个新的名字，可以在其它任何地方使用。 类型别名并不创建新名字—比如，错误信息就不会使用别名。在编译器中将鼠标悬停在 interfaced上，显示它返回的是 Interface，但悬停在 aliased上时，显示的却是对象字面量类型

3. 类型别名不能被 `extends`和 `implements`（自己也不能 `extends`和 `implements`其它类型）

```typescript
type Alias = { num: number }
interface Interface {
    num: number;
}
declare function aliased(arg: Alias): Alias;
declare function interfaced(arg: Interface): Interface;
```

```typescript
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
```

### 函数类型接口

有2种方式

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}
let mySearch: searchFunc;
// 第2种方式
let add: (source: string, subString: string) => boolean;
// 必须参数，可选参数、默认参数，剩余参数
function buildName(firstName: string, lastName?: string, age = 12, ...restOfName: string[] ) {}
```

### 对象类型接口

```typescript
interface ClockInterface {
    currentTime: Date;
    name: string;
}
interface Result {
  data: ClockInterface[];
}
```

### 混合类型接口

```typescript
interface Lib {
  (): void;
  version: string;
  doSomething(): void;
}

function getLib() {
  let lib: Lib = (() => {}) as Lib;
  lib.version = '1.0';
  lib.doSomething = () => {};
  return lib;
}
let lib1 = getLib();
lib1();
lib1.doSomething();
```

### 接口约束类

类必须实现接口中限定的所有属性，可以增加新的属性

```typescript
interface Lib {
  eat(): void;
  name: string;
}

class Man implements Lib {
  name: string;
  age: number;
  eat() {}
}
```

### 接口继承接口

```typescript
interface Shape {
    color: string;
}
interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}
let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

注意：

1. 继承多个接口时，这些接口中的属性不能存在名字一致，但是类型不一致的情况，否则会报错
2. 可以新定义属性，但不能重写继承接口中的同名属性并且使类型不一致。

### 接口继承类

```javascript
class Control {
    private state: any;
}
interface SelectableControl extends Control {
    select(): void;
}
class Button extends Control implements SelectableControl {
    select() { }
}
class TextBox extends Control {
    select() { }
}
// 错误：“Image”类型缺少“state”属性。
class Image implements SelectableControl {
    select() { }
}
```

每当使用一些变量调用 isFish时，TypeScript会将变量缩减为那个具体的类型，只要这个类型与变量的原始类型是兼容的。

```typescript
// 以下都不会报错
if (isFish(pet)) {
    pet.swim();
}
else {
    pet.fly();
}
```

## 泛型

**泛型函数**

```typescript
function identity1<T>(arg: T): T {}
function identity2<T>(arg: Array<T>): Array<T>) {}
function identity3<T, k>(arg1: T, arg2: K): T {}

identity1(10); // 用到类型推断，某些情况可能无法推断
identity1<string>('hello'); // 直接指定泛型类型是string
```

```typescript
// 带有调用签名的对象字面量来定义泛型函数
interface GenericIdentityFn<T> {
    (arg: T): T;
}
function identity<T>(arg: T): T {
    return arg;
}
let myIdentity: GenericIdentityFn<number> = identity;
```

**泛型类**

类的静态成员不可以添加泛型

```typescript
class Log<T> {
  run(value: T) {
    return value;
  }
}

let log1 = new Log<number>(); // 实例化时，指定泛型
let log2 = new Log(); // 实例化时，不指定泛型，则value可以为任意类型
```

**泛型约束**

T extends Lengthwise表示T不管是什么类型，必须拥有过Lengthwise的所有属性

```typescript
// 泛型约束
interface Lengthwise {
    length: number;
}
function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}
```

## 类型检查机制

### 类型断言

**普通断言**

```typescript
interface Bar {
  foo: number;
}
let foo = {} as Bar;
let foo = <Bar>{}
```

! **非空断言操作符**

在上下文中当类型检查器无法断定类型时，一个新的后缀表达式操作符 `!` 可以用于断言操作对象是非 null 和非 undefined 类型。**具体而言，x! 将从 x 值域中排除 null 和 undefined 。**

```typescript
function myFunc(maybeString: string | undefined | null) {
  // Type 'string | null | undefined' is not assignable to type 'string'.
  // Type 'undefined' is not assignable to type 'string'. 
  const onlyString: string = maybeString; // Error
  const ignoreUndefinedAndNull: string = maybeString!; // Ok
}
```

### 类型兼容性

**接口兼容**

只比较结构，当Y类型拥有X类型所拥有的所有属性，即使Y类型还有其他的属性，也可以被赋值给X。即成员多的可以赋值给成员少的

```typescript
interface X {
  a: any;
  b: any;
}
interface Y {
  a: any;
  b: any;
  c: any;
}
x = y; // 正确
y = x; // 错误
```

**方法兼容**

1. 参数个数：固定参数可兼容可选参数、剩余参数；可选参数不兼容剩余参数、固定参数；剩余参数可兼容固定参数、可选参数
2. 参数类型
3. 返回类型

```typescript
let a = (p1: number, p2: number) => {};
let b = (p1?: number, p2?: number) => {};
let c = (...args: number[]) => {};
a = b; // 正确
a = c: // 正确
c = a; // 正确
c = b; // 正确
b = a; // 错误
b = c; // 错误
```

```
//tsconfig.json控制选项
"strictFunctionTypes": true
```

**枚举兼容**

枚举和number完全兼容，枚举之间完全不兼容

```typescript
enum Fruit { Apple, Banana }
enum Color { Red, Blue }
let no: number = Fruit.Apple; // 正确
let fruit: Fruit.Banana = 3; // 正确
let color: Color.Red = Fruit.Apple;  // 错误
```

**类兼容**

1. 比较类时，静态成员和构造函数不参与比较，如果实例属性相同，则类兼容
2. 如果类中有私有成员，则类不兼容

### 类型保护

1. `instanceof`  `typeof`

   ```typescript
   enum Type { Strong, Week }
   function getLanguage(type: Type) {
     let lang = type === Type.Strong ? new Java() : new JavaScript();
     if (lang instanceof Java) {
   
     } else {
   
     }
   }
   ```

2. `in`

   

3. is类型保护，类型谓词

   ```typescript
   function isFish(pet: Fish | Bird): pet is Fish {
       return (<Fish>pet).swim !== undefined;
   }
   ```

## namespace

随着 ES6 的广泛应用，现在已经不建议再使用 ts 中的 `namespace`，而推荐使用 ES6 的模块化方案

命名空间是为了解决重名问题，命名空间的变量、函数、类默认是私有的，若需要在外部访问，需要使用`export`。

```typescript
namespace SomeNameSpaceName {
   export interface ISomeInterfaceName {      }
   export class SomeClassName {      }
}
```

```javascript
// 调用命名空间内的类
SomeNameSpaceName.SomeClassName;
```

如果命名空间在另一个 定义命名空间的TypeScript 文件中使用，使用三斜杠 /// 引用，也可以将命名空间当成模块`export`，使用时`import`

```typescript
/// <reference path = "SomeFileName.ts" />
```

## 声明合并

1. interface合并
   1. interface可以重复定义，必须满足所有interface的全部属性
   2. 普通属性重复：类型必须相同，否则报错
   3. 方法属性重复，按照以下优先级：方法是字面量参数 > 后定义的interface > 先定义的interface （同一个interface中先定义的优先级高）

2. 命名空间和方法/类合并（即同名），命名空间必须放在方法/类的后面，则相当于给方法/类增加静态属性

## declare

用于编译时的检查，在编译结果中会被删除。通常把声明语句放到一个单独的文件中，必须以`.d.ts`为后缀

ts 会解析项目中所有的 `*.ts` 文件，当然也包含以 `.d.ts` 结尾的文件。所以当我们将 `jQuery.d.ts` 放到项目中时，其他所有 `*.ts` 文件就都可以获得 `jQuery` 的类型定义了。

假如仍然无法解析，那么可以检查下 `tsconfig.json` 中的 `files`、`include` 和 `exclude` 配置，确保其包含了 `jQuery.d.ts` 文件。

全局变量声明有以下几种：

- `declare var` 声明全局变量
- `declare function`声明全局方法
- `declare class`声明全局类
- `declare enum`声明全局枚举类型
- `declare namespace`声明（含有子属性的）全局对象
- `declare module`：声明模块

声明模块：

如果是需要扩展原有模块的话，需要在类型声明文件中先引用原有模块，再使用 `declare module` 扩展原有模块

```ts
// 扩展ag-grid-community模块
import { ColGroupDef, ValueGetterParams } from 'ag-grid-community';
declare module 'ag-grid-community' {
  export interface ColumnTypes {
    [key: string]: ColDef;
  }
}
```

## 高级类型

### keyof

用来返回一个class类型或者interface类型的所有key组成的联合类型

key any number | string |symbol

```typescript
type Point = { 0: number; height: number };
interface Point2{name: string; age: number}

type KeyOfPoint = keyof Point; // 0 | 'height'
type KeyOfPoint2 = keyof Point2; // 'name' | 'age'
```

```typescript
function prop<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];
}
function prop2<T>(obj: T, key: keyof T) {
    return obj[key];
}

let o = {
    p1: 0,
    p2: ''
}

let v = prop(o, 'p1') // 返回值is number, K is of type 'p1'
let v2 = prop2(o, 'p1') // 返回值is number | string, no extra info is captured
```

### 索引类型

T[k] 表示对象T的属性K所代表的类型。使用索引访问类型来查找另一种类型的特定属性

```typescript
type Person = { age: number; name: string; alive: boolean };
type Age = Person["age"];    
// type Age = number

type I1 = Person["age" | "name"];    
// type I1 = string | number
 
type I2 = Person[keyof Person];    
// type I2 = string | number | boolean

type AliveOrName = "alive" | "name";
type I3 = Person[AliveOrName];  
// type I3 = string | boolean
```

### 条件类型

Conditional Types

```typescript
T extends U ? X : Y;
```

```typescript
// 如果T是联合类型时，需要等同于拆开后的联合类型
（A | B） extends U ? X : y;
(A extends U ? X : y) | (B extends U ? X : y) ;

// 例子
type TypeName<T> = {
  T extends string ? "string" : 
  T extends number ? "number" : "object"
}
type t = TypeName<string | string[]> // "string" | "object"
```


```typescript
interface Animal {
  live(): void;
}
interface Dog extends Animal {
  woof(): void;
}
 
type Example1 = Dog extends Animal ? number : string;      
// type Example1 = number
```

### 映射类型

Mapped Types：批量创建多个属性，映射类型建立在索引签名的语法之上，用于声明尚未提前声明的属性类型

```typescript
type OnlyBoolsAndHorses = {
  [key: string]: boolean; // 含义是：用任意的string去索引OnlyBoolsAndHorses，可以得到boolean类型
};
 
const conforms: OnlyBoolsAndHorses = {
  del: true,
  rodney: false,
};
```
示例：将FeatureFlags的属性都改为boolean类型
```typescript
type OptionsFlags<Type> = {
  [Property in keyof Type]: boolean;
};
type FeatureFlags = {
  darkMode: () => void;
  newUserProfile: () => void;
}; 
type FeatureOptions = OptionsFlags<FeatureFlags>;
          
// type FeatureOptions = {
//    darkMode: boolean;
//    newUserProfile: boolean;
// }
```

属性修饰符：readonly  and  ?，可以使用 - 或＋去掉或加上

```typescript
// Removes 'readonly' attributes from a type's properties
type CreateMutable<Type> = {
  -readonly [Property in keyof Type]: Type[Property];
};
 
type LockedAccount = {
  readonly id: string;
  readonly name: string;
};
 
type UnlockedAccount = CreateMutable<LockedAccount>;
           
// type UnlockedAccount = {
//    id: string;
//    name: string;
// }
```

### 交叉类型

交叉类型是将多个类型合并为一个类型。 这让我们可以把现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性。

如， `Person & Serializable & Loggable`同时是 `Person` *和* `Serializable` *和* `Loggable`。 就是说这个类型的对象同时拥有了这三种类型的成员。

### 联合类型

联合类型表示一个值可以是几种类型之一。 我们用竖线（ `|`）分隔每个类型，所以 `number | string | boolean`表示一个值可以是 `number`， `string`，或 `boolean`。

### Partial

将类型定义的所有属性都修改为可选

```ts
interface Person {
    name: string;
    age?: number;
}
type Partial<T> = {
    [P in keyof T]?: T[P];
}
type Person2 = Partial<Person>;
// Person2 === {name?: string; age?: number}
```

### Required

将类型定义的所有属性都修改为必须

```typescript
type Required<T> = {
    [P in keyof T]: T[P];
};
type Person3 = Required<Person>;
// Person3 === {name: string; age: number}
```

### Readonly

将所有属性定义为只读

```typescript
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
}
type Person4 = Readonly<Person>;
// Person4 === {readonly name: string; readonly age?: number}
```

### Pick

从类型T定义的属性中，选取k指定的一组属性，返回一个新的类型定义。

`K extends keyof T`：作用是限制K的类型只能在`keyof T`中

```typescript
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
}
type Person5 = Pick<Person, 'name'>;
// Person5 === {name: string}
```

### Record

以 typeof 格式快速创建一个类型，此类型包含K指定的一组属性且都是必填，类型是T指定

```typescript
type Record<K extends keyof any, T> = {
    [P in K]: T;
}
type Person6 = Record<'name' | 'age', string>;
// Person6 === {name: string; age: string}
```

### Exclude

把联合类型T中属性过滤掉联合类型U中有的类型

```typescript
type Exclude<T, U> = T extends U ? never : T;
```
```typescript
type T0 = Exclude<'a' | 'b' | 'c', 'b'> // 'a' | 'c'
type T1 = Exclude<string | number | boolean, boolean> // string | number
```

### Extract

从联合类型T中取出在联合类型U中也存在的类型

```typescript
type Extract<T, U> = T extends U ? T : never;
type T0 = Exclude<'a' | 'b' | 'c', 'b'> // 'b'
type T1 = Exclude<string | number | boolean, boolean> // boolean
```

### Parameters

从函数类型的参数中使用的类型构造元组类型

```typescript
declare function f1(arg: { a: number; b: string }): void;
type T3 = Parameters<typeof f1>;
//type T3 = [arg: {
//    a: number;
//    b: string;
//}]                   
```

### Omit

从类型中过滤指定的key，剩余的key组成的类型

```typescript
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;

type T0 = Omit<Person, 'age'> // T0 === { name: string }
```

```typescript
// 重写onOk和onClose属性
type Options = {
  onOk?: (close: Function) => void;
  onCancel?: (close: Function) => void;
} & Omit<ModalProps, 'onOk' | 'onCancel'>;
```

## 编译选项

自动编译单个文件：编译文件时，使用 -w 指令后，TS编译器会自动监视文件的变化，并在文件发生变化时对文件进行重新编译。

```shell
tsc xxx.ts -w
```

```shell
tsc --init # 初始化生成tsconfig.json文件
```

## react + ts

### reactRef

```react
modalRef: React.RefObject<Modal>;
interface RefObject<T> {
  readonly current: T | null;
}
```

### **React**.FC

函数组件

```typescript
type FC<P = {}> = FunctionComponent<P>;

interface FunctionComponent<P = {}> {
  (props: PropsWithChildren<P>, context?: any): ReactElement<any, any> | null;
  propTypes?: WeakValidationMap<P> | undefined;
  contextTypes?: ValidationMap<any> | undefined;
  defaultProps?: Partial<P> | undefined;
  displayName?: string | undefined;
}
```

例子：

```typescript
interface AlertInterface extends React.FC<AlertProps> {
  ErrorBoundary: typeof ErrorBoundary;
}

const Alert: AlertInterface = () => {};
```







  

