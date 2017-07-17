title: Regular学习笔记(二)
date: 2016-08-01 19:47:01
tags: [Regular, NETEASE]
thumbnail: /images/regular-thum.png
---

# PART1. 静态接口

### 一、extend && implement && new

在实例化一个新的Regular组件时我们总是直接使用

    var compnent = Component.extend({options})

事实上这只是其中的一种方法。根据官方API Reference，静态接口有两种：

1. Component 此接口同时属于Regular及其子类
2. Regular 此接口只属于Regular本身（一般作为全局配置参数）

一般情况下，我们会这样做：

```
var Component = Regular.extend({
    template:`
        <div></div>
    `,
    init:function(){},
})
var component = Component.extend({
    init: function(){
        this.supr()
    }
})
```

<!-- more -->

关于supr的介绍，需要查看[https://github.com/ded/klass](ded/klass)。其实就相当于将父类的同名方法执行一遍（是不是也可以理解为mixin进去？）。

如果希望将所有Component的子组件都加一个新的方法应该怎么做呢？这时候就要用到implement:

    Component.implement({
      hello: function( msg ){
        this.supr( msg ) // call the super hello
      }
    })
    
除了extend方法之外，我们经常还会直接实例化一个组件

    var com = new Component({
        data: {}
    })
    
实例化则意味着传入的option将会成为实例的属性，将覆盖extend和implement的同名属性（方法）的定义，并且在创建时无法调用this.supr()。

### 二、关于options

我们在创建组件时传入的option有好多种，在这里统一列一下

- template
- config： 在模板编译之前被调用，一般用来初始化参数，参数为组件的`data`
- init 在模板编译之后调用，一般在此处处理与DOM相关的逻辑
- destroy： 如果需要自定义回收策略（默认已经有了），可以重写destroy方法，但是记得调用`this.supr()`来调用默认的回收方法
- name： 注册组件到父组件（继承的父组件）的命名空间内，使其可以被内嵌使用。不愿意这么做的话可以使用`Component.component('name', superComponent)来进行注册
- events： 在组件初始化之前声明需要绑定的事件


    Regular.extend({
      events: {
        "$init": function(){
          // same in component.init
        },
        "$destroy": function(){
          // same in component.destroy
        }
      }
    })
    
- data： 父组件的data最终会被merge到实例化时传入的data（即父组件的data可被视为缺省的data

### 三、Component.use() 使用插件

一个典型的插件写法应该是这样的：

    function FooPlugin(Componenet){
      Component.implement()// implement method
        .filter()          // define filter
        .directive()       // define directive
        .event()           // define custom event
    }
    
    var YourComponent = Regular.extend();
    
    FooPlugin(YourComponent);   // lazy bind
    FooPlugin(Regular);         // lazy bind to globals

为了统一，所有的Component都內建一个use函数来统一使用插件，上例使用use的写法为

    YourComponent.use(FooPlugin);
    // global
    Regular.use(FooPlugin);
    
# PART2. 实例接口

上一部分所讲到的都是关于原型的一些东西，现在我们来看看实例，所有的实例接口都带有$前缀，不推荐进行重写。接下来我们一个一个来研究一下这些接口。

### 一、$inject

这个方法用于插入组件到指定的位置

    component.$inject(element[, direction])
    
element为节点的id值(或者是false用于移除组件），direction的值可以是以下几个：[top,bottom,after,before]，看DEMO

    compnent.$inject( div ) ||
    component.$inject( div, 'bottom' )
    //resulting html
    <div id="div">
      <div class='child'></div>
      <h2>Example</h2>
    </div>
    
调用component.$inject(fasle)后即将组件从原插入位置移除（但并未销毁），后续可继续inject它。

### 二、$watch && $unwatch

用于注册监听回调：

    component.$watch(expression, callback(newValue, oldValue)
    
一旦表达式的值发生变化，回调函数就会执行。

类似于js定时器，该方法会返回一个watchid，用于$unwatch解除监听（极少使用到，除非想要实现在模板回收前清除某一绑定？？）：

    var id = component.$watch('b', function(){
      alert('b watcher 2');
    })
    
    component.$unwatch(id);
    
### 三、$update

>由于regularjs是基于脏检查，所以当不是由regularjs本身控制的操作(如事件、指令)引起的数据操作，可能需要你手动的去同步data与view的数据. $update方法即帮助将你的data同步到view层.

与react不同，每次state更新以后都会触发UI的rerender，regualr并不会（准确说是并不一定）在你的data变化之后刷新你的视图，你需要你手动的去监听你要改变的数据继而实现UI更新，看DEMO：

    var component = new Regular({
      template: "<h2 ref='h2' on-click={title=title.toLowerCase()}>{title}</h2>",
      data: {
        title: "REGULARJS"
      }
    });
    component.data.title = "LEELUOLEE";
    //=> also log 'REGULARJS', regularjs don't know the value is changed.
    console.log( component.$refs.h2.innerHTML ) 
    // force synchronizing data and view 
    component.$update()
    //=> 'leeluolee'. synchronize now.
    console.log( component.$refs.h2.innerHTML )
    
上一DEMO中的操作等价于

    component.$update("title","LEELUOLEE")
    
$update()的一般使用方法为`component.$update([expr] [, value])`。由于$update()的特殊性，要求传入的表达式必须要有set方法（不过我们一般会用它来更新data变化，默认就带了set方法）。再看一个更普适的DEMO：

    // multiple
    component.$update({
      "user.name": "leeluolee",
      "user.age": 20
    })
    // is equlas to
    component.data.user.name = 'leeluolee'
    component.data.user.age = 20
    component.$update()
    
### 四、$on && $emit && $off

Regular支持为组件绑定自定义事件，配套的还有事件的触发器、事件的解绑。看一个DEMO马上就懂了：

    var component = new Regular();
    
    var clickhandler1 = function(arg1){ console.log('clickhandler1:' + arg1)}
    var clickhandler2 = function(arg1){ console.log('clickhandler2:' + arg1)}
    var clickhandler3 = function(arg1){ console.log('clickhandler3:' + arg1)}
    
    component.$on('hello', clickhandler1);
    component.$on('hello', clickhandler2);
    component.$on({ 
      'other': clickhandler3 
    });
    
    
    component.$emit('hello', 1); // handler1 handler2 trigger
    
    component.$off('hello', clickhandler1) // hello: handler1 removed
    
    component.$emit('hello', 2); // handler1 handler2 trigger
    
    component.$off('hello') // all hello handler removed
    
    component.$off() // all component's handler removed
    
    component.$emit('other');
    
实际应用的时候还可以直接在模板上绑定和触发（见Regular学习笔记）。

### 五、其他接口

剩下的都是一些简单的接口了，列一下就行

- `component.$get(Expression|String)` 获得一个expression的值
- `component.$refs` 获取标记的节点
- `component.$mute( isMute )` 选择是否使组件失效（不参与脏检查），**mute adj. 哑的,沉默的**