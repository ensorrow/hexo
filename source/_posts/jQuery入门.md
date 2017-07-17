title: jQuery入门
date: 2016-03-17 23:37:48
tags: [eeyes, jQuery]
---

### 一、什么是jQuery?

>jQuery: The Write Less, Do More, JavaScript Library

这是来自jQuery官网的介绍，很贴切的说明了它的功能：用更少的代码编写js脚本。jquery本身就是一个js库，它给你提供了很多人性化的api，利用这些api你可以实现所有原生js的工作。

引入jquery库：使用cdn或者直接下载库，这里以百度cdn为例

	<script src="http://apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>

### 二、功能一览

既然说jquery可以完全实现js的所有功能，那么理论上功能是无限的，不过一切功能的实现无非以这几个功能为基础：

1. DOM遍历和DOM操作
2. 事件处理
3. 动画
4. Ajax

前两个相信所有学习过js的童鞋都特别熟悉吧，我们平时用到的大多数功能靠这两个基础功能就可以实现啦。那么接下来让我们分别过一遍吧。

### 三、DOM遍历和DOM操作

什么是DOM呢？DOM全称 Document Object Model（文档对象模型），HTML DOM 是：

- HTML 的标准对象模型
- HTML 的标准编程接口
- W3C 标准

说起来抽象，实际操作一下就知道了

<!-- more -->

js获取DOM对象：

	var obj = document.getElementById('test')

很熟悉吧？用Jquery来实现就是这样

	$('#test')

同时jquery还支持类选择器，后代选择器等等（几乎css支持的它都支持），如

	$('#test div') || $('div#test')

就是这么强大，我们称之为`jquery selector`，就像css选择器一样。

DOM遍历是啥意思呢？就是指你获取了一个DOM元素以后假如你要获取它的父元素或者子元素、兄弟元素等的操作办法。在js里面分别像这样

	var test = document.getElementById('test');
	console.log(test.childNodes);
	//会返回一个由文本节点和元素节点组成的Nodelist数组；父元素就是parentNode,返回单个元素

运行结果图： ![NodeList](/images/NodeList.png)

说一下nodeType，放一张图就懂了 ![nodeType](/images/nodeType.png)

获取兄弟元素：
	
	var test = document.getElementById('test');
	console.log(test.nextSibling);
	//会返回一个文本节点
	test = test.nextSibling;
	while(test.nodeType!=1){
		test = test.nextSibling;
	}
	console.log(test);

挺麻烦的对吧？这时候Jquery就特别好理解了：

	var test = $('#test');
	test.children();//子元素节点，支持传入参数获取特定元素
	test.parent();//父元素节点
	test.siblings();//同级所有节点

还有很重要很重要的一点，jquery获取到的是支持jquery方法的对象，不同与普通DOM对象，可以看看区别

![普通DOM对象](/images/DOMobj.png)
![jquery对象](/images/JQobj.png)

所以以后不要把js的方法用在jquery对象上啦。顺便提一下，DOM查找遍历是挺消耗性能的，如果有多次调用的需求就把查找结果保存在变量里，不要像这样写：

	$('#test').length;
	$('#test').hide();
	$('#test').show();

尽量这样写：

	var test = $('#test');
	test.length;
	test.hide();
	test.show();

### 四、事件处理

获取到DOM元素以后要做什么呢？当然是给它绑定事件啦，在js里我们一般会这样做：

	var test = document.getElementById('test');
	test.onclick = function() {
		alert('get!');
	}
	//或者这样
	test.addEventListener('click', function() {
		alert('get 2!')
		}, false);
	//addEventListener第三个参数涉及到事件流的概念，即事件的冒泡、捕获阶段

addEventListener( type, function, userCapture)默认采用的是冒泡阶段捕获事件(即第三个参数userCapture默认为false)，听起来懵逼，看一个DEMO秒懂：

	var oinner = document.getElementById('inner');
	var oouter = document.getElementById('outer');
	inner.addEventListener('click', function() {
		alert('inner');
	},true)
	outer.addEventListener('click', function() {
		alert('outer');
	},true)
	//结果是点击inner先alert('outer')

DEMO里userCapture为true，则在事件捕获阶段执行，即你点击了inner以后，outer的点击事件先执行了。盗两张图使一下：

![捕获阶段](/images/buhuo.png)
![冒泡](/images/maopao.png)

很好懂吧~

言归正传，说一下jquery的事件处理，还是一如既往的简单：

	$('#test').click(function(){
		alert('hhh');
		})
	//绑定匿名函数
	//或者稍微复杂一点的
	$('#test').bind('click', function() {
		alert('hhhh');
		})

jquery支持非常非常多的事件，具体的看你们的需求然后查官方文档就行了，这里列一个简单的常用事件：

- focus( ) 元素获得焦点 a, input, textarea, button, select, label, map, area
- blur( ) 元素失去焦点 a, input, textarea, button, select, label, map, area
- change( ) 用户改变域的内容 input, textarea, select
- click( ) 鼠标点击某个对象 几乎所有元素
- dblclick( ) 鼠标双击某个对象 几乎所有元素
- error( ) 当加载文档或图像时发生某个错误 window, img
- keydown( ) 某个键盘的键被按下 几乎所有元素
- keypress( ) 某个键盘的键被按下或按住 几乎所有元素
- keyup( ) 某个键盘的键被松开 几乎所有元素
- load( fn ) 某个页面或图像被完成加载 window, img
- mousedown( fn ) 某个鼠标按键被按下 几乎所有元素
- mousemove( fn ) 鼠标被移动 几乎所有元素
- mouseout( fn ) 鼠标从某元素移开 几乎所有元素
- mouseover( fn ) 鼠标被移到某元素之上 几乎所有元素
- mouseup( fn ) 某个鼠标按键被松开 几乎所有元素
- resize( fn ) 窗口或框架被调整尺寸 window, iframe, frame
- scroll( fn ) 滚动文档的可视部分时 window
- select( ) 文本被选定 document, input, textarea
- submit( ) 提交按钮被点击 form
- unload( fn ) 用户退出页面 window

引用自[kefan_1987的博客](http://blog.163.com/kefan_1987/blog/static/897801312010111461521550/)

### 五、jquery常用方法

获取DOM元素、绑定事件，我们的最终目的都是进行一些实际的操作，比如说改变它的样式、修改它的内容等等，东西非常多，所以要用到的话还是要上网查一下官方文档（用久了的老司机就能都记住了），这里给出一些常用的方法：

- $("p").addClass(css中定义的样式类型); 给某个元素添加样式
- $("img").attr({src:"test.jpg",title:"test Image"}); 给某个元素添加属性/值，参数是map
- $("img").attr("title", function() { return this.src }); 给某个元素添加属性/值
- $("元素名称").html(); 获得该元素内的内容（元素，文本等）
- $("元素名称").html("<b>new stuff</b>"); 给某元素设置内容
- $("元素名称").removeAttr("属性名称") 给某元素删除指定的属性以及该属性的值
- $("元素名称").removeClass("class") 给某元素删除指定的样式
- $("元素名称").text(); 获得该元素的文本
- $("元素名称").text(value); 设置该元素的文本值为value
- $("元素名称").toggleClass(class) 当元素存在参数中的样式的时候取消,如果不存在就设置此样式
- $("input元素名称").val(); 获取input元素的值
- $("input元素名称").val(value); 设置input元素的值为value
- $("元素名称").after(content); 在匹配元素后面添加内容
- $("元素名称").append(content); 将content作为元素的内容插入到该元素的后面
- $("元素名称").appendTo(content); 在content后接元素
- $("元素名称").before(content); 与after方法相反
- $("元素名称").clone(布尔表达式) 当布尔表达式为真时，克隆元素（无参时，当作true处理）
- $("元素名称").empty() 将该元素的内容设置为空
- $("元素名称").insertAfter(content); 将该元素插入到content之后
- $("元素名称").insertBefore(content); 将该元素插入到content之前
- $("元素").prepend(content); 将content作为该元素的一部分，放到该元素的最前面
- $("元素").prependTo(content); 将该元素作为content的一部分，放content的最前面
- $("元素").remove(); 删除所有的指定元素
- $("元素").remove("exp"); 删除所有含有exp的元素
- $("元素").wrap("html"); 用html来包围该元素
- $("元素").wrap(element); 用element来包围该元素

引用自[kefan_1987的博客](http://blog.163.com/kefan_1987/blog/static/897801312010111461521550/)

### 六、实战演练

实现一个简单的计数器功能：

HTML部分：

	<span>0</span>
	<button>加1</button>

js部分

	<script src="./jquery.min.js"></script>
	<script type="text/javascript">
		$(document).ready(function (){
			var ospan = $('span');
			var obutton = $('button');
			obutton.click(function (){
				var tmp = ospan.text();
				tmp = parseInt(tmp);
				tmp+=1;
				ospan.text(tmp);
			});
		})
	</script>

怎么样，有没有感觉到jquery很棒！👀