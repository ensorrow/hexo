title: 前端入门-HTML+CSS基础
date: 2016-09-19 13:08:07
tags: [HTML, CSS]
---

前端最最基础的东西就是静态页面制作，而要制作一个好的静态页面，要求我们熟练掌握HTML+CSS，今天就让我们一起来学习一下这两门前端最基础的语言。

参考教程： 

1. [W3School](http://www.w3school.com.cn/html/)
2. [菜鸟教程](http://www.runoob.com/html/html-tutorial.html)
3. [CSS教程](http://www.w3school.com.cn/css/)

## 一、HTML

关于HTML，首先我们得先了解一下它的全称：Hyper Text Markup Language，即超文本 **标记语言** 。HTML并不是一种编程语言，你不能使用它写出任何可以跑的程序，你只是使用它所提供的一套 **标记标签** （markup tag）来描述你的页面，然后把它交给浏览器去解析。也就是说HTML是不需要你去编译的（包括后面的css和JavaScript也是），你甚至可以直接打开windows自带的记事本，用HTML规范的语言写出一个网页然后直接用浏览器打开就能看到正确的结果（当然还是用专业的文本编辑器比较合适。。）。

好吧，对于一个刚刚接触编程的人上面的话可能不好理解，等你以后学过c或者其他编程语言之后再回来看就可以明白上面这段话的意义了。我们先直接看一个什么入门教程都会有的helloworld页面，看看一个HTML页面长什么样：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <p>Hello <br> World!</p>
</body>
</html>
```

这是一个最为简单的HTML页面结构，从上到下大概可以分为这么几个部分：

- 文档类型声明
- HTML内容部分
    - head标签，包含文档的头信息
    - body标签，包含文档的内容部分

暂且忽略head和类型声明，我们直接从body部分开始入手。

<!-- more -->

### 1.标签与元素

标签，英文 `tag` 。如你所见，在之前那个HTML页面里头出现了大量的尖括号 `<>`包裹起来的东西，这个东西就是一个标签。你也一定注意到了，一部分标签是成对的，如`<p></p>`，一部分标签是不成对的，如`<br>`。这些有什么区别呢？

好，如我们之前所提到的，`<>`尖括号包裹起来的就是一个标签，其中像`<p>`这样的叫做起始标签，`</p>`这种到一个斜杠的叫做结束标签，那么一开一闭就构成了一个 **HTML元素**，如`<p>Hello <br> World!</p>`表示一个段落，其中标签中间的文本就叫做元素的内容。但不是所有的标签都可以构成一个元素，如`<br>`表示一个换行符，它没有结束标签或者说它的结束标签就是它本身，它就只是一个标签而不是一个HTML元素。而且正如你所见，标签是 **支持嵌套** 的。（当然p标签里嵌套p标签就不推荐了，没有意义）

标签可以具有 **属性** ，属性可以让一个标签包含更多的信息。HTML标签属性以名称/值对的形式提供，如`href="http://www.baidu.com"`，且 **定义在开始标签里**。最为典型的如a标签`<a href="#">Link</a>`。

### 2.常用的HTML标签

为了便于理解，这里会先介绍两个比较基础的标签：标题标签和段落标签。（比如在这里就是一个段落标签，上面👆就是一个标题标签）

首先是段落标签p，正如前面Hello World所演示的，直接将一段文本用一个p标签包裹即可得到一个段落：`<p>this is a paragraph</p>`。

然后是标题，标题总共分为六级：h1-h6。也就是说从`<h1>一级标题</h1>`一直到`<h6>六级标题</h6>`，一级标题是最大的标题，六级是最小的。

其他常用的语义化标签有，具体用法请查阅参考教程：

- 无序列表 `<ul></ul>`
- 有序列表 `<ol></ol>`
- 超链接 `<a href=""></a>`
- 图片 `<img src="">`

上面说到的都是一些语义化的标签，事实上，在一个网页中总有那么一些部分你是不能明确认定它是标题或是什么的，所以必然会存在一些没有具体语义的、用于纯布局的标签，最典型的两个就是`<div></div>`和`<span></span>`，关于这两个标签的介绍需要涉及到css布局，等到讲CSS的时候再细讲吧。

### 3.一个简单的DEMO

学过上面的东西以后让我们简单写一个小页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <h1>e瞳第一次前端培训</h1>
    <h3>主讲人：xxx，友情链接：<a href="http://www.eeyes.net">e瞳网</a></h3>
    <img src="http://www.eeyes.net/v20151125/images/camera.png">
    <p>欢迎加入e瞳前端设计组，我们旗下分为两个部门：</p>
    <ol>
        <li>前端组</li>
        <li>设计组</li>
    </ol>
    <h2>PART1. HTML</h2>
    <p>随便填一点内容这是一个段落</p>
    <h2>PART2. CSS</h2>
    <p>随便填一点内容这是一个段落</p>
</body>
</html>
```

## 二、CSS

### 1.修改样式的几种方法

好了，学过HTML之后你会发现完全不够，你写出来的页面长得太丑了，而且你完全无法掌控它的样式，所以接下来我们就要学习如何修改一个页面的样式了。在那之前，先了解一下有哪几种修改样式的方法。

第一种，也是最推荐的那一种，就是引入外部样式表，即引入css文件的方法修改样式，如下：

```html
<link rel="stylesheet" type="text/css" href="./css/style.css">
```

然后直接在对应路径的css文件中写样式就可以了。

第二种，不是很推荐但有时很方便的方法，直接修改标签的style属性，又称为修改行内样式，如下：

```html
<p style="font-size: 16px;">some text</p>
```

第三种，是在head里写一个style标签，里面写样式就可以了，如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style type="text/css">
        p{
            font-size: 16px;
        }
    </style>
</head>
<body>
    
</body>
</html>
```

### 2.css选择器

上面提到的几种方法里最好理解的应该是修改行内样式了吧，直接修改你所需要该样式的标签的style属性就能够实现你的目的，那么另外两种方法呢？如何选中你所想要的标签呢？这里就要用到css选择器了。

基础的css选择器有三种：元素选择器、类选择器、ID选择器。

首先是元素选择器，元素选择器特别简单，直接选择相应的标签名即可：

```css
p{
    font-size:16px;
}
h1{
    color:#fff;
}
```

好的，现假设有如下情况：下面的一个列表里第一、三、四项需要修改样式，元素选择器是不是就懵逼了？这个时候需要用到 **类选择器**，分为两步进行：首先给你想要加样式的标签加一个class属性，然后再使用`.class_name`来选中对应类名的标签，如下：

```html
<ul>
    <li class="special">list item1</li>
    <li>list item2</li>
    <li class="special">list item3</li>
    <li>list item4</li>
    <li class="special">list item5</li>
    <li>list item6</li>
</ul>
```

```css
.special{
    color: #ccc;
}
```

而有的时候你只想选中一个，那就没有必要使用类选择器了，转而使用 **ID选择器**，同样也是两步进行：首先给你要选中的标签加上对应的ID，然后再使用`#id_name`选中：

```html
<ul>
    <li>list item1</li>
    <li id="special">list item2</li>
    <li>list item3</li>
    <li>list item4</li>
    <li>list item5</li>
    <li>list item6</li>
</ul>
```

```css
#special{
    color: #ccc;
}
```

好了，知道了这三种基础的选择器以后很容易就能理解其他的选择器了，都是根据它们衍生出来的，在这里我只是简单地列一下，具体请查阅参考教程：

- 关系选择器
    - 后代选择器
    - 相邻兄弟选择器
    - 子元素选择器
- 属性选择器
- 伪类选择器
- 伪元素选择器
- *

### 3.css基础样式

在修改样式时常有的需求无非就是修改背景、修改字体、修改大小等等，在这里我们通过一个html文档来简单的列一下，很简单，不详述。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div id="wrapper">
        <p>hello world</p>
        <ul>
            <li>hhhh</li>
            <li><a href="#">link here</a></li>
            <li>zzzzz</li>
        </ul>
    </div>
</body>
</html>
```

背景：

```css
#wrapper{
    width: 200px;
    height: 200px;
    /*背景颜色 */
    background-color: #ccc;
    /*背景图片 */
    background-image: url(http://www.eeyes.net/v20151125/images/camera.png);
    background-repeat: no-repeat;
    background-position: 0 10px;
    /*背景样式缩写 */
    background: url(http://www.eeyes.net/v20151125/images/camera.png) #ccc no-repeat 0 10px;
}
```

文本（注意一下font-family）：

```css
#wrapper p{
    font-size: 16px;
    font-family: Times, TimesNR, 'New Century Schoolbook',
     Georgia, 'New York', serif;
    color: #fff;
    text-align: center;
}

```

其他常用：

```css
ul{
    list-style: square;
    /*列表样式*/
}
a{
    color: #000;
}
a:hover{
    color: #ccc;
    /*链接悬浮样式*/
}
```

### 4.css布局

这一块需要当面讲。。
