title: Angular学习笔记（一）
date: 2016-11-05 16:45:07
tags: [Angular]
---

## 一、Angular指令

与vue、regular中的指令类似，Angular也有一套自己的指令系统，而且更为强大（Angular自我介绍的时候就是说用指令来拓展HTML）。本质上指令还是HTML属性，以`ng-`开头，下面大概介绍一下几个简单的指令。

- `ng-app` 该指令声明一个angular app（或者模块）的根元素，在网页加载完毕后，ng-app指令会自动初始化应用（模块）
- `ng-init` 用于初始化变量，实际应用一般会在controller里，基本用不上吧
- `ng-repeat` 类似于list

## Angular依赖注入

wiki上的解释：依赖注入（Depandency Injection）是一种软件设计模式，在这种模式下，一个或更多的依赖（或服务）被注入（或者通过引用传递的方式）到一个独立的对象中，成为该对象状态的一部分。

这种模式的好处就是可以让程序更加的模块化，一个“服务”只需要实现自己的功能，业务逻辑相关的东西写在controller里而与其无关的基本的一些东西（比如说HTTP请求，本地存储等）都可以设计成一个独立的服务，在业务逻辑需要的时候进行注入。

Angular提供一下5个核心组件用来 作为依赖注入：

- value
- factory
- service
- provider
- constant

这几个的使用场景分别是啥呢？我们来一个一个说。

### value

首先是value，与其名称相一致，用于在配置阶段向一个controller传递值（值的类型应该是随意的吧）

```javascript
var app = angular.module("myapp", []);
app.value('mydefault', 'helloworld');
app.controller('ctr', function($scope, mydefault) {
    $scope.yname = mydefault;
})
```

这里顺便提一下，一个controller拥有独立的作用域，控制一个模块（类似于组件？）。那这里和直接声明`var mydefault = 'helloworld'`有啥区别呢？我猜，既然叫做注入，那这个mydefault的作用域应该是在app内部吧，而且必须在参数里显示的声明他，具体实现机制以后用熟了再说。。

### factory

factory就是工厂函数啦。首先科普一下啥叫工厂函数，所谓工厂呢，就是要生产东西的，如果把基本的数据类型理解成原材料的话，那工厂函数就是能够用这些基本类型造出（或者说拼出？）一个新的类型（集合）出来，所以叫它工厂函数。

在Angular里，factory的角色就是要造出符合实际需要的值，通常我们会在service和controller需要的时候使用factory函数来计算或者返回值，看DEMO

```javascript
app.factory('myService', function() {
    var factory = {};
    factory.addon = function(a, b){
        return a+b;
    }
    return factory;
})
app.controller('ctr', function($scope, myService) {
    console.log(myService.addon(3,5));
})
```

不过呢，如果我用value来注入一个对象的话不就实现了和这个同样的效果了么。。。有点搞不懂，用着用着应该就明白了。

### service

在Angular里，你可以自建服务，也可以使用它內建的常用服务，如

```javascript
var app = angular.module('myApp', []);
app.controller('customersCtrl', function($scope, $location) {
    $scope.myUrl = $location.absUrl();
});
```

上面演示了如何便捷的使用內建的服务，下面还要再看看怎么自建服务

```javascript
app.service('MyService', function() {
    this.func = function(x) {
        return x*x;
    }
})
```

自建的服务和內建的一样通过参数注入，很简单吧。

### provider

provider用于在配置阶段创建一个service、factory等，需要重写$get来返回对应的。。服务？

### constant

与value类似，只不过定义的是常量而已

```javascript
phonecatApp.constant('constanttest', 'this is constanttest');
```

然后在控制器等地方应用就行了。
