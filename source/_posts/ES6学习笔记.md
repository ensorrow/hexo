title: ES6学习笔记
date: 2016-08-06 15:47:20
tags: [ES6]
---

文章目录：

- [let & const](#letconst)
- [变量的解构赋值](#destructing)
- [字符串扩展](#string)
- [函数扩展](#function)
- [对象扩展](#object)
- [Set & Map数据结构](#setmap)

ES6虽然用的挺久了，但是一直处于半懂不懂的懵逼状态🙃,急需系统的学习一遍。

本文参考：[ECMAScript入门 - 阮一峰](http://es6.ruanyifeng.com/)

## 一、<a name="letconst">let & const</a>

#### 1.关于let

关于let命令，首先看看它的**基本用法**（块级作用域）：

```
{
    let a = 10;
    var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
```

由此例可见，let所声明的变量只在代码块之内有效，而ES6之前只有全局作用域和函数作用域的概念，此例中var所声明的变量全局有效。let的出现使得一个老问题得到了解决：

```
var s = 'hello';
for(var i=0;i<s.length;i++){
    console.log(s[i]);
}

console.log(i); //i=5
```

事实上我们并不需要这个i（仅作计数），这种情况下就造成了变量名的匮乏（认真脸😐）：取完i取j，取完j取k，k都没有了就用index😂。以后我们直接用let就好了。

<!-- more -->

第二个特性是**不存在变量提升**：

```
console.log(foo); // 输出undefined
console.log(bar); // 报错ReferenceError

var foo = 2;
let bar = 2;
```

var是有变量提升的概念的，在预解析时用var声明的变量已经进入对应的作用域里了（只是运行到这一行时赋值），值为undefined，而使用let是不存在这个现象的，在声明之前使用是会报错的，事实上这样更为规范也更好控制。

第三个可能会是我们经常遇到的问题，假设存在下面的情况：

```
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
```

全局通过var定义了一个变量，然后又在块级作用域里头用let定义了一个，那么在let定义之前这个变量的值是哪一个呢（这他妈就很尴尬了）？两个都不合理，所以还抛出错误好了。。

这个概念就是**暂时性死区**。在代码块中，使用let命令声明变量之前，该变量都是不可用的，这在语法上，称为“暂时性死区”（temporal dead zone，简称TDZ）。

>ES6明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

#### 2.关于const

const，全称目测是constant，即常量，使用const可以声明只读的一个常量

```
const PI = 3.1415;
PI // 3.1415

PI = 3;
// TypeError: Assignment to constant variable.
```

常量的值不能被改变，故声明常量时就需要同时把值赋给该常量，不然会报错。

与let类似，const的作用域也是块级的，const也没有变量提升（准确说是常量提升🤔。。）的现象，也同样会存在暂时性死区。

常量并不意味着对应的值不发生改变，对于复合类型的变量，变量名指向数据所在的地址而不是对应地址的值（数据），比如说经常见到的

```
const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // 报错
```

虽说a是只读的，但是a只保存了这个数组的地址，这个数组本身仍然是可写的，更改这个数组没有任何问题，但是对a赋值就是你的不对啦。

总结一下，除了传统的通过var和function声明变量的方法，ES6还添加了let和const命令（后面还会提到有import和class啊），主要的区别在于作用域和变量提升，最后还要提一点，传统的var和function声明的全局变量作为全局对象的属性而存在，**通过let、const、class声明的变量不再是全局对象的属性**。

```
var a = 1;
// 如果在Node的REPL环境，可以写成global.a
// 或者采用通用方法，写成this.a
window.a // 1

let b = 1;
window.b // undefined
```

## 二、<a name="destructing">变量的解构赋值</a>

什么叫解构赋值呢？先来两个基本用法：

```
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3
//数组

var { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
//对象，按照属性名对应赋值
```

传统情况下我们得一个一个手动赋值，代码冗长无趣，现在，终于，解放啦！👻解构赋值还允许我们指定默认值，像这样：

```
var [foo = true] = [];
foo // true
```

默认值还可以是表达式，不过要注意的是表达式是*惰性求值*，就是说只有在用到的时候才会执行这个表达式。另外还有一个特别要注意的点，*对于对象的解构赋值,如果要先声明，后赋值的话，为了区分对象与代码块，需要使用小括号包裹赋值语句*，如：

```
let baz;
({bar: baz} = {bar: 1}); // 成功
```

对象和数组一样是可以嵌套的，但是不同的是， 并不是每一级的变量都会被赋值，上级属性可能只是一个模式（架子），见DEMO：

```
//ES6
var node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};
var { loc: { start: { line }} } = node;
//ES5
"use strict";

var node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};
var line = node.loc.start.line;
```

在这个栗子中，仅line是变量，其他的都是模式，console.log的话会得到ReferenceError:loc is not defined。

不仅仅是数组和对象，字符串也可以进行解构赋值，不过是作为一个类数组对象：

```
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```

## 三、<a name="string">字符串拓展</a>

新增的部分方法增强了对中文的支持，这个暂且用不到。然后这几个可以看一下：

- includes(),startsWith(),endsWith() 用于判断字符串在另一字符串中的位置，返回布尔值
- repeat(),将某一字符串重复几次输出
- padStart(),padEnd() 用于字符串补全：`'1'.padStart(10, '0') // "0000000001"`

上面的拓展只是为了方便起见，没什么特别大的惊喜，下面这个新特性就令人亦可赛艇了：模板字符串。

以前我们要输出一个模板，需要大量的字符串拼接处理，像下面这样：

```
$('#result').append(
  'There are <b>' + basket.count + '</b> ' +
  'items in your basket, ' +
  '<em>' + basket.onSale +
  '</em> are on sale!'
);
```

实在是烦不胜烦，现在我们就方便多了：

```
$('#result').append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```

不仅可以作为普通字符串使用，模板字符串甚至还能够嵌入变量：

```
//used as normal string
console.log(`string text line 1
string text line 2`);

//嵌入变量
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```

顺带一提如果希望输出不保留换行，使用trim()方法就可以了。在${}内部我们可以放入任意的js表达式，甚至还能嵌套哦！

模板字符串另一个大用处，就是**标签模板**，它可以紧跟在一个函数名后面，这个函数将被用来处理这个模板字符串，也就意味着你能在参数里进行复杂的运算处理啦。

## 四、<a name="function">函数的扩展</a>

第一个是参数默认值功能，这个很早之前用RN的时候就用过了😆

```
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```

参数默认值还可以和解构赋值结合起来，像这样

```
function fetch(url, { body = '', method = 'GET', headers = {} }) {
  console.log(method);
}

fetch('http://example.com', {})
// "GET"

fetch('http://example.com')
// 报错
```

>注意：通常情况下，定义了默认值的参数，应该是函数的尾参数。因为这样比较容易看出来，到底省略了哪些参数。**如果非尾部的参数设置默认值，实际上这个参数是没法省略的**，除非显式输入undefined。

ES6还引入了rest参数，用于获取多余的参数（以前的解决方法貌似是使用特殊变量arguments

```
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}
```

rest参数必须为最后一个参数。与rest参数对应的有扩展运算符`...`，将一个数组转为用逗号分隔的参数序列。

```
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5
```

然后是我们的**箭头函数**：

```
var f = v => v;
//equal to
var f = function(v){
    return v;
};
```

如果有多个参数且代码块部分有多条语句，可以用括号括起来：

```
var sum = (num1, num2) => { return num1 + num2; }
```

箭头函数与变量解构结合起来用：

```
const full = ({ first, last }) => first + ' ' + last;
// 等同于
function full(person) {
  return person.first + ' ' + person.last;
}
```

使用箭头函数的第一个大优势就是this的绑定。在箭头函数内部是没有自己的this的，导致了内部的this就是外层代码块的this，把箭头函数等价的用ES5写出来就是这样：

```
// ES6
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

// ES5
function foo() {
  var _this = this;

  setTimeout(function () {
    console.log('id:', _this.id);
  }, 100);
}
```

这个特性使得其在组件化的时代尤为吃香，因为它可以让this指向组件本身：

```
var handler = {
  id: '123456',

  init: function() {
    document.addEventListener('click',
      event => this.doSomething(event.type), false);
  },

  doSomething: function(type) {
    console.log('Handling ' + type  + ' for ' + this.id);
  }
};
```

## 五、<a name="object">对象扩展</a>

ES6支持对象属性和方法的简写：

```
var birth = '2000/01/01';

var Person = {

  name: '张三',

  //等同于birth: birth
  birth,

  // 等同于hello: function ()...
  hello() { console.log('我的名字是', this.name); }

};
```

ES6还支持使用表达式作为属性名和方法名，表达式需要放在方括号内

```
var lastWord = 'last word';

var a = {
  'first word': 'hello',
  [lastWord]: 'world'
};

a['first word'] // "hello"
a[lastWord] // "world"
a['last word'] // "world"
```

接下来会有一个常用的方法`Object.assign(target, ...source)`用于对象的合并，第一个参数是目标对象，后面的参数都是源对象。要注意的是该方法实行的是**浅拷贝**，目标对象拷贝的只是源对象的引用：

```
var obj1 = {a: {b: 1}};
var obj2 = Object.assign({}, obj1);

obj1.a.b = 2;
obj2.a.b // 2
```

对于对象，我们经常需要对它的属性进行遍历，ES6一共提供了5种方法：

（1）for...in

for...in循环遍历对象自身的和继承的可枚举属性（不含Symbol属性）。

（2）Object.keys(obj)

Object.keys返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含Symbol属性）。

（3）Object.getOwnPropertyNames(obj)

Object.getOwnPropertyNames返回一个数组，包含对象自身的所有属性（不含Symbol属性，但是包括不可枚举属性）。

（4）Object.getOwnPropertySymbols(obj)

Object.getOwnPropertySymbols返回一个数组，包含对象自身的所有Symbol属性。

（5）Reflect.ownKeys(obj)

Reflect.ownKeys返回一个数组，包含对象自身的所有属性，不管是属性名是Symbol或字符串，也不管是否可枚举。

>symbol是程序创建并且可以用作属性键的值，并且它能避免命名冲突的风险。调用Symbol()创建一个新的symbol，它的值与其它任何值皆不相等。它是ES6提出的JavaScript的第七种原始类型。

## 六、<a name="setmap">Set和Map数据结构</a>

ES6新增了两种数据结构Set和Map（以前貌似只有Array和Object），来使我们的各种数据操作更加便利。首先我们来介绍一下Set。

#### 1. Set

Set类似于数组，但是要求所有的成员值是唯一的，不能出现重复的值：

```
var s = new Set();

[2, 3, 5, 4, 5, 2, 2].map(x => s.add(x));
s //Set {2,3,5,4}
for (let i of s) {
  console.log(i);
}
// 2 3 5 4
```

Set构造函数可以接受一个数组作为参数，只是会剔除掉其中的重复值。要注意在Set中重复成员的意义为两个值精确相等`===`（除了NaN自身相等）。

Set实例的属性和方法列举如下：

- *实例属性*
- `Set.prototype.constructor`：构造函数，默认就是Set函数。
- `Set.prototype.size`：返回Set实例的成员总数。
- *操作方法*
- `add(value)`：添加某个值，返回Set结构本身。
- `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `has(value)`：返回一个布尔值，表示该值是否为Set的成员。
- `clear()`：清除所有成员，没有返回值。
- *遍历方法*
- `keys()`：返回键名的遍历器
- `values()`：返回键值的遍历器
- `entries()`：返回键值对的遍历器
- `forEach()`：使用回调函数遍历每个成员

(暂时没get到新数据结构的价值。。)

#### 2.Map

暂时用不到，以后补充。
