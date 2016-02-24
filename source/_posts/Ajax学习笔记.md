title: Ajax学习笔记
date: 2016-02-20 17:05:47
tags: jQuery Ajax
---

我想跟后台打交道的东西是很多前端程序猿的软肋，至少我看到的好多都是这个样子的。之前对表单数据交互，Ajax，Promise一直是一知半解，那么趁着刚开学的这一段时间来补充一下这方面的知识。

## 一、post请求和get请求的区别

首先看看百度怎么说的

>   GET使用URL或Cookie传参。而POST将数据放在BODY中。
    GET的URL会有长度上的限制，则POST的数据则可以非常大。
    POST比GET安全，因为数据在地址栏上不可见。

作为一个前端，能够经常接触到这个的大概就在`form`里。一个标准的from标签如下：

	<form action="#" method="get"></form> ||
	<form action="#" method="post"></form>

这两者方法有什么区别呢？看看来自W3C的解释：

> post方法：
如果采用 POST 方法，浏览器将会按照下面两步来发送数据。首先，浏览器将与 action 属性中指定的表单处理服务器建立联系，一旦建立连接之后，浏览器就会按分段传输的方法将数据发送给服务器。
在服务器端，一旦 POST 样式的应用程序开始执行时，就应该从一个标志位置读取参数，而一旦读到参数，在应用程序能够使用这些表单值以前，必须对这些参数进行解码。用户特定的服务器会明确指定应用程序应该如何接受这些参数。

>get方法
浏览器会与表单处理服务器建立连接，然后直接在一个传输步骤中发送所有的表单数据：浏览器会将数据直接附在表单的 action URL 之后。这两者之间用问号进行分隔。

了解到这个地步就差不多啦

<!--more-->

## 二、jQuery Ajax使用

在AJAX出现之前，貌似所有的HTTP请求都是同步的，意味着你填一个表单，必须点一下提交，然后收到反馈（类似于单线程计算机？）。那么AJAX就可以让我们通过发送异步请求来及时获得反馈，更加的高效和人性化。

Api采用的是图灵机器人的api，[机器人接入](http://www.tuling123.com/web/robot_access!index.action?cur=l_05)，访问这个api后，以问好为例，会直接返回json格式的数据

	{
		"code": 100000,

		"text": "你也好 嘻嘻"
	}

而且没有跨域的问题（不晓得怎么实现的😂），代码如下，很简单

	$(document).ready(function(){
		var TURING = 'http://www.tuling123.com/openapi/api?key=YOUR_OWN_KEY&info=你好&userid=12345678';
		$.ajax({
			type: 'get',
			url: TURING,
			dataType: 'json',
			//dataType应该默认就是json，在这里可有可无
			beforeSend: function(xhr) {
				//showloading
			},
			success: function(data, textStatus) {
				console.log(data.text);
			},
			complete: function(xhr, textStatus) {
				//hideLoading
			},
			error: function() {
				alert('error loading ');
			}
		});
	});

就会在控制台输出机器人给你打招呼的信息啦。

#### Ajax跨域问题

提到跨域问题，首先就要提到同源策略

>同源策略，它是由Netscape提出的一个著名的安全策略。现在所有支持JavaScript 的浏览器都会使用这个策略。所谓同源是指，域名，协议，端口相同。当一个浏览器的两个tab页中分别打开来 百度和谷歌的页面当一个百度浏览器执行一个脚本的时候会检查这个脚本是属于哪个页面的，即检查是否同源，只有和百度同源的脚本才会被执行。

所以当你用Ajax请求某一个不同源（跨域）的API时，浏览器会拒绝运行你的脚本，所以你就抓取不到想要的数据了。那么如何解决这个问题呢？

我们都知道在我们使用`script`标签引入外部js时，就算不同源也是没有问题的（比如CDN?)，这是因为`script`标签并不被同源策略所束缚，可以获取任何服务器的脚步并执行。根据这个原理，就出现了一种叫做JSONP的东西，英文名为JSON With Padding，它是怎么实现的呢？

简单的概括就是说，通过服务器上的操作，使得`script`请求完以后，将你所请求的**数据传入**你自定义方法名的**方法**中完成回调。可以看一个DEMO：

	<script type="text/javascript"> 
	    //添加<script>标签的方法 
	    function addScriptTag(src){
	        var script = document.createElement('script');
	        script.setAttribute("type","text/javascript");
	        script.src = src;
	        document.body.appendChild(script);
	    }

	    window.onload = function(){
	        //搜索apple，将自定义的回调函数名result传入callback参数中
	        addScriptTag("http://ajax.googleapis.com/ajax/services/search/web?v=1.0&q=apple&callback=result");

	    }
	    //自定义的回调函数result
	    function result(data) {
	        //我们就简单的获取apple搜索结果的第一条记录中url数据
	        alert(data.responseData.results[0].unescapedUrl);
	    }
	</script>

那么在jQuery Ajax中已经帮你把jsonp直接封装好了，直接把dataType改成jsonp，jQuery会自动把数据传入你默认的callback中（也就是success方法吧？），当然，你还可以自定义jsonpCallback。找到了一个支持jsonp的api，写一个DEMO测试一下：

	$(document).ready(function(){
		var POSTCODE = 'http://www.geonames.org/postalCodeLookupJSON?postalcode=10504&country=US&callback=?';
		$.ajax({
			type: 'get',
			url: POSTCODE,
			dataType: 'jsonp',
			beforeSend: function(xhr) {
				//showloading
			},
			success: function(data, textStatus) {
				console.log(data.postalcodes[0].adminCode2);
			},
			complete: function(xhr, textStatus) {
				//hideLoading
			},
			error: function(err) {
				alert('error loading '+'err');
			}
		});
	});

正如我们想的那样，这个请求的结果是把api对应的数据传入success方法中进行执行。然后直接在浏览器中请求这个api（在地址栏里输入）得到的是这样的：

	?({"postalcodes":[{"adminCode2":"119","adminCode1":"NY","adminName2":"Westchester","lng":-73.700942,"countryCode":"US","postalcode":"10504","adminName1":"New York","placeName":"Armonk","lat":41.136002}]});

也就是说callback的方法名是？（jquery会智能分配到success,如果没有自定义的话），然后这个方法里面的参数就是我们要的数据啦~😀

关于Promise的过两天再更新吧~