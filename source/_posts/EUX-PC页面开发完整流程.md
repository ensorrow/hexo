title: EUX PC页面开发完整流程
date: 2016-05-21 17:43:31
tags: [EUX, Vue]
---

鉴于小伙伴们开发过程无从下手，这里写一篇完整前后端开发流程的文档。在开发之前，请将完整代码clone到本地，执行npm install安装模块依赖（貌似还需要全局安装gulp和配置python环境），安好数据库，做好mongorestore，能够正常运行以后进入我们的开发流程（这些过程详见上一篇文档）。

### 一、前端部分开发流程

目前开发集中在/static/admin这个目录下进行。admin下有这几个东西：

- main.js, app的路由配置信息等
- build.js, webpack打包以后的app主文件（忽略它）
- /components, 用于存放自定义Vue组件的目录, 一般将可复用的部分抽象为组件
- /containers, 用于存放页面容器（外壳）, 在容器里引用、拼凑组件

现在分析一下各目录下现有的文件，首先看一下`components`，里面有这些文件

- common.vue，公共头部和侧边栏组件
- introbox.vue，成员管理界面复用的个人信息组件

然后看一下`containers`

- app.vue，app的主入口文件
- Concat.vue，员工信息容器组件
- Grade.vue，绩效考评容器
- Work.vue，作业考评容器

好了，现在开始我们的前端开发流程，我们的开发大概分为以下几步：

#### 1.在main.js配置路由跳转信息

在这里我们的几个项目已经帮你配好了，分别是`/work,/grade,/concat`，分析一下代码：
	
	import App from './containers/app.vue'
	import Concat from './containers/Concat.vue'
	import Grade from './containers/Grade.vue'
	import Work from './containers/Work.vue'

	router.map({
	    '/work': {
	        component: Work
	    },
	    '/grade': {
	        component: Grade
	    },
	    '/concat': {
	        component: Concat
	    }
	});

由于我们做的是SPA单页面应用，这些路由对应的组件其实都嵌套在一个最高级的父级容器里

	router.redirect({
	    '*': '/work'
	});

	router.start(App, '#app')

也就是说默认跳转到/work下面，最高级容器为App.vue，#app为App组件对应的挂载点（在/app/views/admin.html里）

	<body>

	<div id="app"></div>
	<script src="/admin/build.js"></script>
	<script src="/dist/js/admin/message.min.js"></script>
	</body>

如果要新建项目的话也就是自己新建一个组件，然后在router.map里加一项就行啦~

#### 2.使用vue写静态页面

一个vue组件大概是这个样子的

	<template>
		<div>test</div>
	</template>
	<style>
		div{
			background-color: #fff;
		}
	</style>
	<script type="text/javascript">
		export default{
			data(){
				return {
					//该组件的数据放在这里
				}
			},
			method: {
				//自定义方法写在这里
			}
		}
	</script>

然后你在其他地方引用你的组件

	import Component from '../components/test.vue'

在需要使用组件的地方把它注册成一个自定义标签

	import Vue from 'vue'
	Vue.component('my-com',Component)

在template里引用就行啦

	<my-com></my-com>

组件里如果使用静态资源的话，把你的静态资源（如图片）放到`/static/src/admin`下，最好一个项目放一个目录，然后在组件里引用编译以后的资源`/dist/admin`即可。

>这只是一个大概的流程，请抽空将vue.js文档完整过一遍

### 二、后端开发流程

前后端通过api进行数据交互，通过controller制作api接口。在开发后端之前，确保你明白post请求和get请求大致区别😂。先看一个例子吧：

	router.get('/schk', selfcheck);
	//员工自主申请
	function selfcheck(req, res) {
	  res.render('user/selfcheck', {
	    title: '员工自主验证',
	    user: req.session.user
	  })
	}

>未完待续