title: 开始使用Redux(一)
date: 2016-02-22 20:08:13
tags: [React Native, Redux]
---

第一次接触这种涉及到产品架构的框架，整体的理解难度简直爆表，今天来根据RN中文网的这一篇帖子[用RN( ListView + Navigator ) + Redux来开发一个ToDoList](http://bbs.reactnative.cn/topic/26/%E7%94%A8rn-listview-navigator-redux%E6%9D%A5%E5%BC%80%E5%8F%91%E4%B8%80%E4%B8%AAtodolist/2) 来分析一下如何在一个RN应用中使用Redux框架。

首先将`action`,`reducer`,`store`这些概念再过一遍（引用来自Redux 中文文档的解释）：

### 1. Action

>Action 是把数据从应用（译者注：这里之所以不叫 view 是因为这些数据有可能是服务器响应，用户输入或其它非 view 的数据 ）传到 store 的有效载荷。它是 store 数据的唯一来源。一般来说你会通过 store.dispatch() 将 action 传到 store。

也就是说action是传到store的唯一参数，只有给store dispatch了一个action，才能改变store；一般来说action是用来描述操作的js普通对象，像这样：

	{
	  type: COMPLETE_TODO,
	  index: 5
	}

### 2. Reducer

Action用来描述事情发生的这一事实，然后需要通过Reducer来指明需要如何来改变state，Reducer所做的事情就像这样

>(previousState, action) => newState

这个直接看DEMO就行了

<!--more-->

	module.exports = function(state, action) {
		state = state || {
			type: 'INITIAL_TODOS',
			todos: []
		}

		switch(action.type) {
			case 'LOAD_TODOS': {
				...
			}
	        case 'APPEND_TODO': {
	            var todos = [ ...state.todos ];
	            todos.unshift(action.todo);
	            dataSource = state.dataSource.cloneWithRows(todos);
	            return {
	                ...state,
	                ...action,
	                todos,
	                dataSource
	            }
	        }

	        case 'SELECT_TODO': {
	            ...
	        }
		}	

		return {
			...state
		}
	}

用一个switch...case来判断action.type，返回对应的新state以及依应用而变的参数。

### 3. Store

Store是用来将action和reducer联系起来的一个由框架提供的特殊对象，store负责这些事情

- 维持应用的state
- 提供getState()方法获取state
- 通供dispatch(action)方法更新state
- 通过subscribe(listener)注册监听器

那么如何根据现有的action和reducer创建对应的store呢?

	import { createStore } from 'redux'
	import reducer from './reducers'
	let store = createStore(reducer)

如果要用到异步的话（肯定会用到的），就用一个`redux-thunk`框架

	const createStoreWithThunk = applyMiddleware(thunk)(createStore);
	const reducer = combineReducers(reducers);
	const store = createStoreWithThunk(reducer);

这样就创建好store了。但是还不够，因为redux不是特地为React开发的，要想在RN上用还得用到React绑定库`react-redux`

>注意：由于不知名的原因（本文开头链接里有），react-redux版本不能超过3.x，否则会报错 unable to resolve modules form react-redux/native，在这里我们用到的是3.0.1版，请修改package.json自行安装。

安装好以后做这几件事情：

##### 1. 从绑定库中引入你想要用的模块（废话）

我们要用到connect模块

	import { connect } from 'react-redux/native';

##### 2. 链接store与应用主入口

这里操作可能有点难理解，但是大概是这三步

第一步，新建一个函数，该函数返回参数作为state，具体作用是把上一个App入口传入的store里的state取到并返回

	function selector(state) {
	    return {
	        state: state
	    }
	}

第二步，做一个connect链接主应用文件与取到的state，具体作用是将取到的state作为BaseApp的props来传递

	export default connect(selector)(BaseApp);

第三步，利用Provider把主应用文件包装起来，把store作为props传进去

	<Provider store={store}>
		{() => <BaseApp />}
	</Provider>

这样的话准备工作就算做完啦~

---

好了，做完了准备工作，那么现在要干什么呢？当然是获取state，修改state啦。

具体操作还是下回分解吧，我困了👀