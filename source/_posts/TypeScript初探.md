title: TypeScript初探
date: 2017-07-17 09:20:01
tags: [TypeScript]
---

>TypeScript is a typed superset of JavaScipt that compiles to plain JavaScript

这是来自其官网的介绍，简单的来说呢，他就是一个可以编译成js的，兼容js的，具有类型的编程语言。至于什么叫具有类型呢？众所周知，js是一门弱类型的语言，我们简单的通过var关键字进行变量的声明然后赋值，并不关心其类型。TypeScript强化了类型的概念。（下文中将使用ts指代TypeScript。）

## 一、基础类型与变量的声明

js的基础类型分为两类，原始类型(primitive type)和对象类型(object type)。原始类型有数字、字符串、和布尔值，null和undefined也可以计在内；对象类型中比较特殊的有数组和函数。（参考《JavaScript权威指南》）。

ts既然自称为typed js，在类型上肯定也是做了扩充的，简单的列一下它支持的类型：

1. 布尔：`let isDone: boolean = false;`
2. 数字：`let decLiteral: number = 6;`（其他进制也可以）
3. 字符串：`let name: string = "bob";`
4. 数组：`let list: number[] = [1, 2, 3];`or使用泛型`let list: Array<number> = [1, 2, 3]`
5. 元组（Tuple）：`let x:[number, string] = [10, 'hello'];`（元组越界后会涉及到联合类型这一概念）
6. 枚举（Enum）：`enum Color {Red, Green, Blue}; let c:Color = Color.Green;`
7. Any：在编程阶段还不清楚类型的变量，使用any可以跳过编译阶段的审查
8. Void：表示没有任何类型，一般用于函数返回值为空
9. Null和Undefined：ts中两者各自为一种类型，默认情况下他们也是所有类型子类型（严格模式不是）
10. Never：表示永不存在的类型，一般用于无限循环或抛出异常的函数的返回值类型

ts中有类型断言这一概念，意味着你十分确定某一变量的类型，编译器将会简化其类型检查流程（暂时理解）。有两种形式进行：

```javascript
// 尖括号形式
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;
// as语法
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

变量的声明这一块，和ES6一样，我们使用let和const代替了var，具体的含义也都是和ES6一致的。ES6中的变量的解构赋值也应用的很多。（详情可见[变量声明](https://www.tslang.cn/docs/handbook/variable-declarations.html)）

<!--more-->

## 二、接口（Interface）

>TypeScript的核心原则之一是对值所具有的结构进行类型检查。 它有时被称做“鸭式辨型法”或“结构性子类型化”。 在TypeScript里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。

接口最基础的应用就是在函数传参时进行参数的检查，也是很实用的一个应用：

```javascript
interface LabelledValue {
  label: string;
}
function printLabel(labelObj: LabelledValue) {
  console.log(labelObj.label);
}
let myObj = {label: "size"};
printLabel(myObj);
```

提到函数传参，自然要想到可选参数的问题了，我们可以使用?来表征可选属性：

```javascript
interface SquareConfig {
  color?: string;
  width?: number;
}
```

值得注意的是，可选属性意味着属性的所有可能性，所以如果出现了可选属性之外的属性时是会报错的，如果你要绕过这个报错的话，可使用的方式有三种吧：

```javascript
// error: 'colour' not expected in type 'SquareConfig'
let mySquare = createSquare({ colour: "red", width: 100 });
// 使用类型断言
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);
// 推荐：字符串索引签名
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
// 将对象赋值给另一个变量，原理据说是该变量不会经过额外属性检查
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```

接口还可以描述**函数的类型**，只给代码不详述：

```javascript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string) {
  let result = source.search(subString);
  return result > -1;
}
```

上面提到过字符串索引签名，实际上接口可以描述**可索引的类型**，如a[10] or ageMap['louis']，可索引的类型具有一个**索引签名**，用来描述索引（数组就是index）的类型，支持字符串和数字：

```javascript
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```

更为广泛的用法，接口用来明确的强制一个类去符合某种契约，接口会描述类的公共部分：

```javascript
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date);
}
class Clock implements ClockInterface {
  currentTime: Date;
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) { }
}
```

## 三、类

ts摒弃了js奇怪的原型链形式，实现了ES6的类，简单介绍一个使用类的例子。

```javascript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```

类的继承

```javascript
class SubGreeter extends Greeter {
  constructor(msg: string) {
    super(msg);
  }
  greet() {
    console.log('sub');
    super.move();// 在方法中调用super.method()表示调用基类同名方法
  }
}
```

类的一些描述符: public, private, protected，老生常谈啦，简单的可认为鄙视链 public > protected > private。另外还有一个readonly描述符，字面意思。

类的setter和getter: 一般情况下，我们直接访问类的属性来进行设置或读取，若需要中间操作的话，可以考虑使用get和set：

```javascript
class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            console.log("Error: Unauthorized update of employee!");
        }
    }
}
let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

还有类的静态属性（只在实例化时才被初始化，不可修改），抽象类就不赘述啦。

## 四、函数

ts为js函数添加了额外的功能以便于使用，具体体现在：

1. 为函数定义类型

```javascript
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x+y; };
```

一般函数返回值类型我们不用写，ts会自动推断（ts里除了能够推断的其他都要写类型，思维不能还停留在js那里）。

2. 函数的完整类型

```javascript
// 完整例子
let myAdd: (x:number, y:number) => number =
  function(x: number, y: number): number { return x+y; };
// 指定一边
// myAdd has the full function type
let myAdd = function(x: number, y: number): number { return x + y; };

// The parameters `x` and `y` have the type number
let myAdd: (baseValue:number, increment:number) => number =
  function(x, y) { return x + y; };
```

3. 可选参数、默认参数、剩余参数

在js中，我们可以少传参数，没穿的会默认为undefined，ts要求会更加严格，要实现可选参数需要声明：

```javascript
function buildName(firstName: string, lastName?: string) {
    ...
}
```

默认参数和剩余参数就很熟悉了：

```javascript
function buildName(firstName = 'Louis', ...restOfName: string[]) {
  ...
}
```

## 五、泛型

什么是泛型呢？在软件工程中，我们考虑让组件支持不同的数据类型，这个时候就可以使用泛型来创建可重用组件，什么意思呢，看个例子：

```javascript
function identity(arg: number): number{
  return arg;
}
```

如果我们想让它支持更多的数据类型，那貌似吧number修改成any就行了，但是这个时候会出现一个问题，我怎么知道这两个any是同一个类型呢，不添加额外的判断代码是做不到的，所以这个时候泛型就可以起作用了：

```javascript
function identity<T>(arg: T): T {
  return arg;
}
```

在上面这个例子中，我们使用了一个特殊的**类型变量**T，这样的一个特殊的、支持多个数据类型的函数，我们称它为泛型。定义了泛型函数以后，我们可以像平常那样去调用它（会有自动的类型推断），也可以手动去传入类型变量：

```javascript
let output = identity<number>(2);
```

当然一般情况下是无必要的，我们选择更为简洁的类型推断方式。接下来考虑一种使用场景，我们知道泛型的变量是一个数组，但是由于使用了泛型（话说你知道是数组你还用泛型），你在调用数组的方法过程中是会报错的，因为泛型意味着多种可能性，如下：

```javascript
function test<T>(arg: T):T {
  console.log(arg.length); // Error: T doesn't have .length
  return arg;
}
```

改造一下，控制数组的元素就好了：

```javascript
function test<T>(arg: T[]): T[] {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```

接下来我们关注一下泛型函数本身的类型(有点绕)，上面讲函数的时候我们描述过函数的完整类型，使用泛型以后就相当于加一个类型参数

```javascript
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
```

我们还可以使用带有调用签名的对象字面量来定义泛型函数：

```javascript
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: {<T>(arg: T): T} = identity;
```

这个我们使用接口来描述它的话，就是这样：

```javascript
interface GenericIdentityFn {
    <T>(arg: T): T;
}
// recomended
interface GenericIdentityFn<T> {
  (arg: T): T;
}
```

既然都有泛型接口了，那就再讲一下泛型类，非常简单：

```javascript
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
```

更复杂的情况下，我们对类型变量进行约束，让泛型的范围稍微在窄一些，也就是所谓的**泛型约束**：

```javascript
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}
```

定义了约束以后，如果传入的参数没有length属性就会报错啦。

TypeScript还有许许多多的特性，比如枚举啊，Iterator和Generator啊，这些用到的时候再查就行了，上面讲的这些应该够日常开发使用了，笔芯❤️。