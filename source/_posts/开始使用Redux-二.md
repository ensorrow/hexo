title: 开始使用Redux(二)
date: 2016-02-24 21:54:04
tags: [React Native, Redux]
---

上一篇文章分析了一下Redux各个部分的定义、功能，今天我们就要考虑一下怎么在实际项目中使用这个state管理框架了

>在应用中，只有最顶层组件是对 Redux 可知（例如路由处理）这是很好的。所有它们的子组件都应该是“笨拙”的，并且是通过 props 获取数据。

这个做法与我们现在的做法是一致的，但是按照我们一般的props传递方法，当这个组件嵌套的比较深的时候，可能就会发现要把这个数据传进去出奇的麻烦，所以很自然的我们就需要一个全局的对象，在任何嵌套的组件访问这个对象，就可以获取任何你所想要的属于全局的数据。Redux所提供的store就是充当了这一角色,现在来看看store在App中是怎么生效的。

### 一、根组件绑定Redux以及通过props传递

怎么绑定在上一篇文章中已经提过了，绑定完了以后根组件的state以后就充当着全局的store了。首先，通过Provider把store通过props传下去：

	<Provider store={store}>
		{() => <BaseApp />}
	</Provider>

BaseApp里通过路由再把state和action传下去：

	renderScene(route, navigator) {
	    let Component = route.component;
	    const { state, dispatch } = this.props;
	    const action = bindActionCreators(actions, dispatch);
	    return (
	        <Component 
	            state={state}
	            actions={action}
	            {...route.params} 
	            navigator={navigator} />
	      );
    }

<!-- more -->

这里用到了`binActionCreators()`,关于这个函数可以参考一下官方说明：

>惟一使用 bindActionCreators 的场景是当你需要把 action creator 往下传到一个组件上，却不想让这个组件觉察到 Redux 的存在，而且不希望把 Redux store 或 dispatch 传给它。

### 二、子组件调用回调函数

首先要获取回调函数，也就是action，通过props获取：`const { appendTodo } = this.props.actions;`获取完以后再执行：`appendTodo(text);`这个时候绑定了Redux的根组件就会调用对应的回调函数（通过Reducer）：

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

这里return的就是新的state（不要觉得只有第一个...state才是😂），然后Redux应该会有一个机制使得传递到当前组件的props同步更新，触发UI更新（有待实践）。

未完待续，在实践中再发现哪些还需要分析