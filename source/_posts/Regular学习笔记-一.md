title: Regular学习笔记(一)
date: 2016-08-01 19:03:41
tags: [Regular, NETEASE]
---

>regular笔记：Regular是一个类似于react，vue的轻量框架

## 一. 模板语法

首先要知道模板的语法类型，总共只有两种语句：功能语句&插值语句

功能语句就像这样：

    {#list Expression as Var } ... {/list}
    
有开有闭，开闭符号貌似是可以自定义的。

插值语句是传统的mustache，直接`{ Expression }`就行了。

所有的expression在编译之后会成为一个表达式对象，使用`$expression`来访问它，表达式对象有两个部分：get()&set()

- ### get(context)

看DEMO比较好懂

     // 定义一个组件
      var component = new Regular({
        data: {
          user: 'leeluolee',
          age: 20 
        }
      });
      // 通过$expression传入自定义参数创建组件的表达式对象实例
      var expr = component.$expression("user + ':' + (age - 10)")
      // 调用实例的get方法，传入组件对象
      alert(expr.get(component) === "leeluolee:10" ) //true
      
- ### set(context, value)

同样还是看DEMO，很好理解

    // component as context
      var component = new Regular({
        data: {
          users: [
            {name: 'leeluolee'},
            {name: 'luobo'}
          ],
          index:0
        }
      });
    
      var expr = component.$expression('users[index].name')
    
      expr.set(component, 'daluobo');
    
      alert(component.data.users[0].name === 'daluobo') // true
      
## 二. 指令

和vue类似，regular也有一套自己的指令系统。当rugular解析到一个属性，首先会去查是否是一个注册指令（预设or自定义），是则交由指令的**link函数**，否则视为普通属性。首先看看如何自定义指令。

### 定义指令

每一个组件可以通过`Component.directive`来扩展指令，其中的核心为link函数

#### link(element, value, attrs)

element为指令绑定的节点，value可以传入值或表达式，attrs指节点上的属性列表，如

    Regular.directive('r-html', function(elem, value){
      this.$watch(value, function(newValue){
        elem.innerHTML = newValue
      })
    })
    
如果希望销毁这个函数，只需要在link函数执行末返回一个新的函数

    return function(){
        //新的link函数
    }
    
### 内建指令（预设）

- r-model（数据双向绑定）
- r-style（行内计算样式）
- r-class（如判断class是否为active）
- r-hide（节点是否展示）
- r-html（类似于xss）
- r-animation（声明式动画）

具体使用时查阅api文档。

指令被用来代替一些常用的DOM操作，但还有一些DOM操作是指令不便于胜任的，regular使用`Regular.DOM`实现了一部分诸如DOM插入、DOM删除的功能

#### Regular.dom.inject(element, refer, direction)

DOM插入操作。element指要被插入的节点，refer指参考节点，direction指组件插入的位置，有‘top’, ‘bottom’, ‘after’, or ‘before’四个参数可选。

#### Regular.dom.on(element, event, handle)

这个方法中的event对象已被修正（兼容IE6-8），回调也被修正为element对象本身。见DEMO：

    var dom = Regular.dom;
    
    dom.on(element, 'click', function(ev){
      ev.preventDefault();
    })
    
其他的dom操作比较简单，列一下就行了

- Regular.dom.off(node, event, handle)
- Regular.dom.addClass(element, className)
- Regular.dom.delClass(element, className)
- Regular.dom.text(element[, value])
- Regular.dom.html(element[, value])
- Regular.dom.attr(element, name [ , value])

## 三. 组件

组件 = 模板 + 数据 + 业务逻辑，是一种独立的，可复用的交互元素的封装。

组件的定义很简单，直接看DEMO：

    var Component = Regular.extend( options );
    var SubComponent = Component.extend( options );
    
    var sub = new SubComponent;
    var component = new Component;
    
    console.log(sub instanceof Regular) // true
    console.log(sub instanceof Component) // true
    console.log(sub instanceof SubComponent) // true
    
options对象有以下几个属性：

- **template**
- config
- init
- destroy
- name
- **event**
- data

其中的name是指该组件注册到父组件命名空间的名称，以便于在父组件中内嵌使用

    var Component = SuperComponent.extend({
      //other options
      name: 'foo1'
    })
    
    var Component2 = SuperComponent.extend({
      template: "<foo1></foo1>"
    })
    
也可以通过`Component.component`来注册

    var Component = SuperComponent.extend({});
    //第一个参数为注册名称，第二个为父组件
    SuperComponent.component('foo1', Component)
    
其他的参数自行查阅[API文档-静态接口-options](http://regularjs.github.io/reference/?api-zh#api-reference-静态接口-options)。

#### 组件初始化

定义了一个组件之后，可以以两种方式进行初始化。比如说定义一个Pager组件：

    var Pager = Regular.extend({
      name: 'pager'
      // other options
    })
    
进行初始化的两种方法分别如下：

##### 1. 通过js进行初始化

    var pager = new Pager({
        data: {
            current: '1'
        }
    })
    pager.$on('nav', someCallback)
    
##### 2. 直接在模板中进行初始化

    <pager current=1 on-nav={someCallback($event)} />
    
在模板中的初始化实际上使用了options里的name属性，可以使用`Component.component`自定义，这种方法即所谓的内嵌组件。

#### 内嵌组件

内嵌组件通常会传入一些属性，这些属性会成为data的成员，如

    <pager total=100 current={1} show={show} ></pager>
    
则data.tatal = '100', data.current = 1 & pager.data.show = this.data.show (this => 模板所在组件)。若属性没有值则默认为true，属性名若有连字符则转为驼峰式，如

    <pager show-modal={true} isHide={hide}/>
    
使用data.showModal访问show-modal属性。on-[eventName]自动转换为组件事件绑定：

    <pager on-nav={this.nav($event)}></pager>
    
相当于`pager.$on('nav', this.nav.bind(this))`。还有两个特殊属性ref & isolate，到时候再说。

## 四. 事件

在Regular中，事件分为两类

- DOM事件
- 组件事件

#### DOM事件

节点上的以on-开头的属性均会被作为DOM事件处理，如`on-click={Expression}`。regular在这一部分的实现原理为：如果遇到on-xx属性，首先检查是否注册自定义事件，若无，则使用addEventListener来绑定事件，所以js所有原生事件都可以得到很好的支持。自定义DOM事件（如判断键盘某个键被按下）的方法如下：

    Component.event(event, fn)
    
event为自定义事件名称，fn为对应执行的函数，包含两个参数`fn(elem, fire)`

- elem: 绑定节点
- fire：触发器

直接看DEMO：

    var dom = Regular.dom;
    
    Regular.event('enter', function(elem, fire){
      function update(ev){
        if(ev.which == 13){ // ENTER key
          ev.preventDefault();
          fire(ev); // if key is enter , we fire the event;
        }
      }
      dom.on(elem, "keypress", update);
      return function destroy(){ // return a destroy function
        dom.off(elem, "keypress", update);
      }
    });
    
    // use in template
    <textarea on-enter={this.submit($event)}></textarea>`
    
事件绑定可以用on-，同时也可以使用事件代理的方法，即使用delegate-代替on-，一般用于大型列表

    <div delegate-click="remove">Delte</div>   //Proxy way via delegate
    <div delegate-click={this.remove()}>Delte</div> // Evaluated way via delagate
    
事件触发时会有一个特殊的$event变量存储在data中，可以在模板中直接使用它，如：

    new Regular({
      template:
      "<button on-click={this.add(1, $event)}> count+1 </button> \
        <b>{count}</b>",
      data: {count:1},
      add: function(count, $event){
        this.data.count += count;
        alert($event.pageX)
      }
    }).$inject(document.body);
    
$event对象是经过修正的，在兼容IE6前提之下可以提供使用的属性有：

- origin: 绑定节点
- target: 触发节点
- preventDefault(): 阻止默认事件
- stopPropagation(): 阻止事件冒泡
- which
- pageX
- pageY
- wheelDelta
- event: 原始事件对象

#### 组件事件

什么叫组件事件呢？举个例子就明白了：组件的生命周期。Regular集成了Emmiter，所有组件可以使用以下接口实现事件驱动的开发：

- component.$on: 用于添加事件监听器
- component.$off: 用于解绑事件监听器
- component.$emit: 用于触发某个事件

组件的事件绑定与DOM事件绑定很类似，只是一个是作用于组件一个作用于DOM而已，如：

    //???
    <div>
        <pager on-nav={ this.refresh($event) }></pager>
    </div>
    
组件的生命周期分三个阶段：

1. $config: 会在compile之前触发，你可以在此时预处理组件数据
2. $init : 会在compile之后触发，此时，dom结构已经生成，你可以通过ref来获取了
3. $destroy: 会在组件被destroy时，触发

无论是DOM事件还是组件事件，处理事件的方法都是一致的，有两种方法：

- 使用表达式（推荐）

使用表达式则事件触发后表达式会被运行一次

    <div on-click={this.remove(index)}>Delte</div>
    
remove被定义在组件当中

    var Component = Regular.extend({
      template:'example',
      remove: function(index){
        this.data.list.splice(index ,1);
        // other logic
      }
    })
    
- 非表达式（函数名）

传入的不是表达式时，事件会被代理至组件的事件系统当中，使用$on来处理对应的事件

    <div on-click="remove">Delte</div>
    
    var Component = Regular.extend({
     template:'example',
     init: function(){
       this.$on("remove", function($event){
           // your logic here
       })
     }
    })
    
## 五. 过滤器与计算属性

#### 过滤器

与Python中的过滤器类似如：

    <p>{ [1,2,3] | join: "+" } = 6</p>
    //过滤器结果为"1+2+3"
    
过滤器一部分是预设的，自定义过滤器的方法如下：

    Component.filter(name, factory)
    
其中factory为函数对象，有get和set两个方法，直接传函数的话转换为factory.get。双向过滤器以后再看。

#### 计算属性

计算属性与vue的计算属性几乎完全一致。传入的计算属性可以是三种类型的：

1. 包含get/set的对象
2. 直接传入函数作为getter
3. 传入字符串表达式作为Expression对象

一个一个看吧。首先是对象类型的计算属性：

    var component = regular.extend({
      computed: {
        fullname: {
          //传入data属性，返回处理后的属性fullname
          get: function(data){
            return data.first + "-" + data.last;
          },
          //fullname设置器，传入希望的值及data属性，设置data.first、data.last从而get到fullname为对应值
          set: function(value, data){
            var tmp = value.split("-");
            data.first = tmp[0];
            data.last = tmp[1];
          }
        }
      }
    })
    
然后是函数类型的计算属性，函数直接被作为getter函数存在，没有setter：

    var component = regular.extend({
      computed: {
        fullname: function(data){
            return data.first + "-" + data.last;
        }
      }
    })
    
最后是字符串表达式，直接被处理为Expression对象

    var component = regular.extend({
      computed: {
        fullname: "first+ '-' + last"
      }
    })
    
## 贴几个自己跟着写的小DEMO

可以自己添加item的NoteList

    <div id="app"></div>
	<script type="text/javascript" src="regular.min.js"></script>
	<script type="text/javascript">
		var Note = Regular.extend({
			template:
			"<input type='text' r-model={draft}>"+
			"<button on-click={this.post()}> post </button>",
			post: function(){
				var _data = this.data;
				this.$emit('post', _data.draft);
				//post事件触发时draft作为参数$event，用到了组件事件知识点
				_data.draft = '';
			},
			data: {
				draft: ''
			},
			name: 'note'
		});
		var Notelist = Regular.extend({
			template:
			//note组件的post事件触发时执行后面的表达式
			"<note on-post={ items.push({ content: $event }) } />"+
			"<ol>{#list items as item}"+
			"<li class={item.done?'done':''} on-click={item.done = !item.done}>{ item.content }</li>"+
			"{/list}</ol>"
		});
		var notelist = new Notelist({
			data: {
				items: [
					{content: 'game'},
					{content: 'homewok'}
				]
			}
		}).$inject('#app');
	</script>
	
-----

### 拓展

- 数据绑定与脏检查
- 基于类的数据绑定（klass，supr）