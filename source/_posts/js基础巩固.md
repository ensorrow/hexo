title: js基础巩固（一）
date: 2016-04-30 22:41:04
tags: [EUX, js]
---

学习一门语言，基础是很重要的。今天花一点时间研究记录一下js那些基础、原理性的小东西。

### 一.原始类型和对象类型

js有两种基本类型，第一类是基本（值）类型，第二类是对象（引用）类型，首先看一下基本类型

- Undefined
- Null
- Number
- String
- Boolean

然后是引用类型

- Date
- Array
- Regexp
- Function
- Object
。。。

>引用类型之所以叫**引用**类型是因为对象在栈内存中只保存它在堆内存中的地址

<!-- more -->

所以这个时候就会发生下面的事情

	var c = {x: 1};
	var d = c;
	d.x = 2;
	console.log(c.x);//2

因为指针指向的堆内存中的值已经被修改啦~😄

>提到引用先插一句，js函数传参的过程都是采用的值传递方法，传递的是参数的值而不是引用（引用类型会复制一份）

关于类型判断，一般有两种方法：`typeof`和`instanceof`，前者用来判断基本类型+function+object（除掉function以外的引用类型加上null），后者用来判断实例与原型关系。

### 二.隐式类型转换

关于隐式类型转换，我们应用最多的场景无非就是`==`了，经常我们会使用`''==false`，`1==true`这样方便的判断来作为if等的条件，那么这背后的原理是什么呢？

隐式类型的转换步骤大概分为五步：

1. 双等号前后存在NaN，则无条件返回false
2. 双等号前后有布尔值，则将布尔值转为数字再判断
3. 双等号前后有字符串，则分为3种情况：对象（转字符串或数字）、数字（字符串转数字）、字符串（直接比较）
4. 双等号前后为数字与对象，对象取 valueOf() || toString()
5. null == undefined

关于对象类型转基本类型，就是两个方法，要么valueOf先转数字，要么toString转字符串，那么作为js引擎它在隐式类型转换的时候怎么知道先调用哪一个方法呢？为了试验一下我们可以重写一下它的valueOf和toString方法：

	var arr = {};
	arr.valueOf = function() {return "1";}
	arr.toString = function() {return "2";}
	console.log(arr == 1);//或者arr == "1"
	//输出为true，可以看到在这里先调用的是valueOf方法

无论你是对比字符串还是对比数字还是对比布尔值，默认都是先调用valueOf，如果valueOf返回值不是一个基本类型（值类型），那么继续调用toString，比如`[9].valueOf()`就是[9]这个引用类型，而不是9这个数字。

### 三、基本包装类型

为了便于操作基本类型值，ECMAScript提供了三个特殊的引用类型：Boolean、Number和 String。这些类型是的我们能够对基本类型进行一些对对象的操作，举个栗子：

	var str = 'sample';
	console.log(str.substring(1,3));

讲道理的话，作为一个基本类型str是没有方法这个东西的，但是substring在这里明摆着就是它的方法啊，怎么回事呢？这个时候基本包装类型就发挥作用啦，在调用这个方法的时候，实际上会自动有这样一步操作：

	var s = new String('sample');
	console.log(s.substring(1,3));

所以如果你想添加基本类型的属性那是不可能的，因为当你设置属性的一瞬间它会调用包装类型然后重写下面的方法，再次获取该属性时又会重新包装一遍，所以还是没有这个属性的，方法也一样。

先写这么多了，困死了😑