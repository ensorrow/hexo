title: EUX开发环境配置
date: 2016-03-10 21:12:17
tags: [EUX]
---

准备工作：Node环境（搜索node.js安装即可） & git环境 & cnpm（自行百度安装）

### 一、安装配置mongoDB

MAC下可以使用Homebrew直接安装（200多m，慢的一比），Windows下貌似也可以直接安装exe。安装完以后自行在根目录（怎么方便怎么建，注意windows下尽量放C盘避免权限问题）下建一个data文件夹，终端运行

	mongod --dbpath /*data文件夹路径*/

然后就会在data文件夹里自动创建好相关的文件并启动数据库，此时打开另一个终端窗口，cd到clone下来的EUX目录下，运行

	mongorestore ./mongobak

数据库就配置完毕了。推荐安装Robomongo来可视化的管理数据库（不然太抽象了😂），貌似Windows，Linux，Mac都有。使用可视化管理工具的方法也很简单，先保证终端的数据库在运行，然后新建一个connect到对应端口就行啦。

### 二、安装node依赖

首先终端cd到eux目录，然后直接运行`cnpm install`。不出意外的话就能安装好，但是一般意外比较多，大概有以下几种错误：

1. node-gyp错误：如果提示找不到命令的话就全局安装它`cnpm install node-gyp -g`.其他编译错误首先到npm官网搜索node-gyp这个包看看它的文档，一般来说可能是电脑上缺少python环境，具体的自己查一下文档
2. （TODO)

然后全局安装gulp，打开终端运行

	cnpm install gulp -g

再进入eux目录执行`gulp`，正常的话再次运行gulp就编译过啦，然后打开localhost:3000就可以看到运行结果了，就是我们酷酷的EUX首页了。

<!-- more -->

### 三、新建个人项目（如果有需要）

**请在dev分支上进行开发！**

1.新建项目目录，路径为app/views（最好在目录下有一个index.html）
2.添加路由信息，路径为config/routers，实际导出一个js数组对象，like this

	module.exports = [
	  { 'url': '/test', 'cpath': 'test.controller.js'}
	]

3.添加对应的controller，路径为app/controllers，内容像这样

	var express = require('express');
	var router = express.Router();

	router.get('/', test);

	function test(req, res, next) {
	  res.render('test/index', {
	    title: 'EUX测试',
	    user: req.session.user
	  });
	}

	module.exports = router;

这样就可以在你自己的项目目录下进行开发了，以test为例，浏览器地址就是localhost:3000/test。

### 四、push到coding上

这步没啥好说的，git push就完了（本地测试得通过啊，而且先git pull保证和最新dev分支没有冲突），推送完以后手动@我（QQ或微信😂），然后让我来帮你merge到master上然后部署到服务器上就行啦~

### 五、关于使用到的技术

电脑端页面（推荐学习路线：Vue => Express => Bootstrap)

1. [Vue.js，数据驱动的组件，为现代化的 Web 界面而生](http://cn.vuejs.org)，web开发框架
2. [Bootstrap，简洁、直观、强悍的前端开发框架](http://www.bootcss.com)，UI框架
3. [Express - 基于 Node.js 平台的 web 应用开发框架](http://www.expressjs.com.cn)，后端框架
4. mongo，非关系型数据库

微信企业号

1. [Koa (koajs) -- 基于 Node.js 平台的下一代 web 开发框架 ](http://koa.bootcss.com)

先学这个，其他的再说
