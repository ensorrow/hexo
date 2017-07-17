title: jQuery类库入门
date: 2016-12-01 12:18:00
tags: [jQuery]
---

参考资料：《JavaScript权威指南》

虽然我们一直说js大法好，js统一世界这样的话，但是在实际使用的过程中，js还是有很多令人不爽的地方的，最显著的就是选择器了，为了选中一个元素我们要写这么长的一串：`var obj = document.getElementById('test')`，而且这么长的一串只能选中一个id为特定值的元素；如果是想通过元素名选中元素得到的还是节点列表（虽然可能我只有一个元素😕），所以在以前我们可能会封装一个函数，比如说：

```javascript
function domSelect(name){
    //逻辑很复杂
}
```

然后我们调用这个函数，传入类似选择器的字符串，返回我们想要的dom节点。又比如说，我想要遍历一个数组，ES5之前，只能用for循环来遍历（摊手）；之后有一个数组多了一个forEach方法，然而这个坑爹方法参数竟然没有index😑，只能获取到每个数组的元素；顺便可以提一下ES6多了好几个遍历的方法，还是很好用的，当然前提是你要忽略它的兼容性。所以这个时候我们会想要一个封装好的方法可以像这样使用:

```javascript
array.each(function(item, index){
    //逻辑处理
})
```

可惜这些简单的需求都要你自己实现，所以这个时候很多强大的类库应运而生。所谓类库呢，就是说给你实现了很多很多功能的类的集合（而且解决了跨浏览器的兼容性）。jQuery是这其中最为出色和著名的一个，下面我们来介绍一下jQuery。

<!-- more -->

## 一、jQuery基础

首先我们先引入jQuery类库，注意需要在你自己的js文件之前引入，我们这里使用CDN来演示：

```html
<script src="http://apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
```

首先jQuery定义了一个全局函数：jQuery()。由于该函数会被频繁的使用，所以我们还给了它一个别名`$()`（记得之前讲过标识符的要求吧？），这两个变量是jQuery在全局命名空间内定义的所有变量。我们使用的所有jQuery方法都会以该函数为基础。首先我们随便演示一下jQuery的风格：

```javascript
$("p.details").css("background-color", "yello").show("fast")
```

虽然你没学过jQuery，但是应该也能一眼看明白我想干什么：先选中类名为details的p标签，然后修改它的样式为背景黄色，然后把它快速的显示出来（所以他本来应该是隐藏的，此处有动画）。是不是感觉这个写法很优雅，很简洁，很符合一般人的书写习惯？

好现在我们简单的讲一下上面这一行代码。首先呢我们往$函数里传入了一个字符串，这个字符串使用了和css选择器一模一样的语法，然后返回了一个jQuery对象，这就是我们要讲的第一个问题，jQuery函数的调用方式。

`jQuery()`的调用方法分为四种，列举如下：

1. 传入选择器字符串，如 `$('p .special')`选中p标签下面的class为special的元素
2. 传入DOM对象，返回包装后的jQuery对象，如`$(document)`返回document的jQuery对象，使其能调用jQuery提供的方法
3. 传入HTML文本字符串，如`$('<p>jquery object</p>`会生成一个相应的jQuery对象
4. 传入匿名函数，会在文档加载完毕后调用，即`$(function() {})`，在旧版本的jQuery里会写作`$(document).ready(function() {})`

上面调用jQuery函数的1、2、3方法均会返回一个jQuery对象（是指能够调用jQuery方法的对象）。现在让我们来看看一个jQuery对象是什么样的:

![jquery对象](/images/jqueryObj.png)

jQuery对象是一个类数组，你可以使用数组下标访问该对象，会得到对应的`DOM对象`（注意不是jQuery对象哦，要获得jQuery对象的话要调用index()方法），也可以通过length属性获取它的长度。在我们的示例中值得注意的是，我们使用了链式调用的方法，即在设置完css的时候又返回了操作的那个jQuery对象，又可以继续对它进行操作。这种链式调用的思想在jQuery中非常常见，用得好的话可以很优雅。

## 二、jQuery的getter和setter

什么是getter什么是setter呢？简单的理解就是，获取值和设置值的方法，举个栗子，在JavaScript中，我们要操作元素内部的HTML：

```javascript
var tmp = domObj.innerHTML // getter
domObj.innerHTML = '<b>hhh</b>'  //setter
```

在jQuery中，很多getter和setter用的是同一个方法，只是调用方式不同（传参不同），下面使用DEMO分别展示。

#### 1. 获取和设置HTML属性

```javascript
$('form').attr('action');// 获取第一个form元素的action属性值
$('form').attr('action', 'http://api.com');// 使用字符串参数设置第一个form元素的action属性值
$('form').attr({action, 'http://api.com'});// 使用键值对参数设置第一个form元素的action属性值
$('form').attr('action', function() {
    if(this.target === '_blank') return 'http://api.com';
    else return '#';
});// 做一次判断
```

值得注意的是，直接操作DOM时，class、for等属性作为保留字，我们访问的时候要用别名，在jquery中就可以随意啦。

#### 2. 获取和设置css属性

```javascript
$('p').css('color');
$('p').css('color', '#ccc');
$('p').css({color: '#ccc', font-size: '16px'});
```

使用方法和操作属性是一致的，getter本质上是获取计算样式（所以不能获取简化样式比如margin、font）,setter本质上是修改style属性。

#### 3. 获取和设置css类

常用的操作有以下几种，给元素添加class、去掉class，判断元素有无该class

```javascript
$('p').addClass('test');// 添加class
console.log($('p').hasClass('test'));// 判断是否有test class
$('p').removeClass('test');// 移除class
console.log($('p').is("p.test"));// 同样用于判断，更强大
```

#### 4. 获取和设置元素的位置宽高

```javascript
$('p').offset();// 获取元素相对于文档左上角偏移，返回{top: number, left: number}
$('p').offset({top: 20, left: 20});// 设置位置，会根据当前位置与目标位置设置偏移量与position:relative

$('p').width();// 获取元素content box宽度
$('p').width('200px');// 设置元素css的width属性
```

## 三、修改文档结构

实际项目中，我们经常会做的一个操作就是修改文档结构，比如修改元素内容就是最简单的一个场景。除此之外，我们还会新增元素、删除元素、替换元素、移动元素、复制元素等。我们一个个说。

首先是插入元素，这里直接复制粘贴代码了，因为一看就懂

```javascript
$("p").append("<b>你好吗？</b>");//向p元素中追加<b> 
$("<b>你好吗？</b>").appendTo("p");//将<b>追加到p元素中 
$("p").prepend("<b>你好吗？</b>");//向p中前置<b> 
$("<b>你好吗？</b>").prependTo("p");//将<b>前置到p元素中 
$("p").after("<b>你好吗？</b>");//向p元素后插入<b> 
$("<b>你好吗？</b>").insertAfter("p");//将<b>插入到p元素后边 
$("p").before("<b>你好吗？</b>");//在p元素之前添加<b> 
$("<b>你好吗？</b>").insertBefore("p");//将<b>插入到p元素前面 
```

注意一下很多都支持链式调用哦。然后替换元素`$("p").replaceWith("<strong>你最不喜欢的水果是？</strong>")`，删除元素`$("ul li:eq(1)").remove()`, `$("ul").empty();//清空ul的内容 `,移动元素`$('#test').appendTo('div');//将#test移动到第一个div中`，复制元素`$('#test').clone().appendTo('div');//将#test拷贝一份移动到第一个div中`。

## 四、jQuery处理事件

一般来说，我们会使用jQuery事件处理程序的 **简单注册方法**，像这样：

```javascript
$('p').click(function() {
    $(this).html('clicked');    
});
```

除了click还有很多其他的事件，如`blur()`、`keydown()`等，具体的请查询文档。

使用jQuery注册事件时可以给所有的选中元素都绑上事件，非常方便，同时众所周知，js事件对象在不同的浏览器实现是有差异的，使用jQuery绑定事件时，事件对象是按照W3C标准来实现的，不用考虑兼容性的问题。

除了简单注册方法，jQuery还有 **高级的事件注册方法**：

```javascript
$('p').bind('click', function() {})
```

高级绑定方法允许绑定多个事件，同时支持事件命名空间（便于批量解绑）。这里只给个DEMO，关于高级事件注册方法更多请自行百度哦☺️

```javascript
$('a').bind('click.mymod', f);
$('a').bind('mouseenter mouseleave', f);
```

和绑定事件处理程序对应，还有 **解绑** 的方法，也就是`unbind()`

```javascript
$('a').unbind('.mymod');// 解绑mymod命名空间下所有事件处理程序
$('a').unbind('mouseenter mouseleave');
```

有的时候我们需要在代码中 **手动触发事件**，这个时候有两种方法，就像简单和高级事件绑定方法一样

```javascript
$('a').click();// 触发a元素的click事件简单版
$('a').trigger('click');// 高级版，可以用于触发自定义事件，自定义事件属于高级用法自行差文档哦😯
```

还有一个比较重要的概念，叫 **实时事件**。考虑这样一种场景，一个巨大的且经常更新的列表，我们如果需要每个列表项都要能监听点击事件，那么每次更新列表都要绑定一次事件，毫无疑问这是非常低效的，在js中我们通常的选择是给父元素绑定事件（概念上称之为事件代理），然后子元素点击以后冒泡上来再判断事件目标，这个时候jQuery提供了一个绑定事件的方法叫`delegate()`完美解决了这一需求，见DEMO

```javascript
$(document).delegate("li", "click", handler)
```

DEMO的含义为，给document绑定一个点击事件，当事件目标匹配第一个参数（选择器）时，触发事件处理程序，十分的方便。

## 五、jQuery动画

在js中我们实现动画都是直接通过动态改css样式属性值来实现的，有一些比如淡入淡出、浮入浮出这些按理说应该预置的基础动画都没有，jQuery就很友好了，常用的动画一应俱全，先演示一下一个简单的淡入动画：

```javascript
$('#ad').fadeIn("slow", function() {
    $(this).text('点我关闭');    
});
```

值得注意的是动画是异步的，所以使用回调或者链式调用来处理逻辑（如果希望是在动画结束之后调用的话），不要使用同步的方法啦。jQuery定义的简单动画总共可以分为3组，如下

- fadeIn(), fadeOut(), fadeTo()
- show(), hide(), toggle()
- slideDown(), slideUp(), slideToggle()

这些方法接收的时间参数可以是fast(200ms),slow(600ms),无参数则400ms，另外可以直接接收数值参数作为时间。

讲完简单动画接下来当然是高级动画了，因为这是入门教程所以高级的只演示基本功能哦：

```javascript
$('#ad').animate({
    opacity: .25,
    color: '#ccc'
}, {
    duration: 500,
    complete: function() {
        //逻辑
    }    
});
```

提一下高级动画的其他一些概念以便你们自行查资料，缓动函数（比如上次说的贝塞尔曲线），动画取消、延迟与队列。

## 六、jQuery中的ajax

原生的js写ajax时，每次都要写那么固定的几个步骤：创建xhr,xhr开启,xhr发送,然后加上事件监听，简直烦不胜烦。jQuery直接封装好了，这样用就行：

```javascript
$.ajax({
    type: 'GET',
    url: 'http://api.net',
    data: null,
    dataType: 'json',
    success: callback
});
```

十分简洁，但是jQuery觉得还不够，又封装一层：

```
$.load('sample.html', {data: 'test'});// 异步加载HTML文档
$.getJSON('sample.json', function(data) {
    //data是json转化后的对象    
})
$.get('http://sample.url', function(data){
    //data是response对象    
})
$.post('http://sample.url', function(data){
    //data是response对象    
})
$(selector).get(url,data,success(response,status,xhr),dataType) //完整示例
```

嗯，入门的话jQuery知道这些就挺不错的啦，另外jQuery还提供了一些工具函数，自己需要的时候查文档使用吧。就酱，顺便mark一下，今天心情不大好呢。☹️