title: ES6异步流程控制
date: 2017-02-09 13:06:16
tags: [ES6]
---

在以往做Node开发的时候，比如说做爬虫和后端，我们会大量使用回调函数，举一个很简单的例子，我们要实现对一个文件夹内容进行读取，读取成功以后将文件夹内的文件进行逐个删除，如果我们使用node的异步方法的话，就得这样

```javascript
fs.readdir('./test/', function(err, files){
    if(err) console.log(err);
    else {
        files.forEach(function(file) {
            fs.unlink('./test/'+file, function(err) {
                if(err) console.log(err);
                else console.log('删除文件成功！');
            });
        });
    }
})
```

上面的例子仅仅是实现了一个简单的需求就嵌套了三层回调，如果我再随便加点异步请求什么的，估计代码嵌套都要突破天际，这是非常不利于代码的可读性和维护的，而且异步的执行顺序搞不清楚的话，自己等下都会被搞晕了，所以在ES6里加入了Promise和Generator来解决这一问题。

## 一、js单线程与异步

众所周知，js的执行环境是单线程的，也就是说一个任务必须等前一个任务执行完了才能执行，那这种情况下异步是怎么实现的呢？

>除了主JavaScript执行进程外，还需要一个在进程下一次空闲时执行的代码队列。随着页面生命周期推移，代码会按照执行顺序添加入队列，例如当按 钮被按下的时候他的事件处理程序会被添加到队列中，并在下一个可能时间内执行。（《高级定时器》P609）

简单的说就是，异步的代码不会被立即执行，而是会插入到一个代码队列的末尾，在所有同步的代码执行完以后，根据同步代码排队插入好的异步代码队列开始按照顺序执行，这就是异步在单线程里的实现，可以简单的写一个程序检验一下我们的观点

```javascript
var startTime = new Date().getTime();
setTimeout(function() {
    console.log('异步代码1执行时间点：'+(new Date().getTime()-startTime));
},0);
setTimeout(function() {
    console.log('异步代码2执行时间点：'+(new Date().getTime()-startTime));
},0);
for(var i=0;i<10000;i++){
    //我是拖时间的
}
console.log('同步代码执行时间点:'+(new Date().getTime()-startTime));
```

执行代码可以看到代码的执行顺序👻[代码执行结果1](./images/async-1.png)

## 二、Promise

Promise本质上是一个对象，我们可以通过它将我们的异步流程进行包装，然后利用它提供的API来控制我们的异步处理流程（异步的各个过程分别使用哪个回调进行处理），比如一个简单的文件读取

```javascript
var readFile = function(path) {
    return new Promise(function(resolve, reject) {
        fs.readFile(file, function(err, data) {
            if(err) reject(err);
            else resolve(data);
        });
    });
}

readFile('./test/1.html')
    .then((data) => console.log(data))
    .catch((err) => console.log(err));
```


简单的解释一下上面的代码，我们利用Promise包装了一个函数，调用`readFile()`后得到了一个Promise实例，在我们调用它的`then`方法时，相当于把原来匿名函数里的`resolve`函数替换成了我们在`then`方法里传入的函数，`catch`对应于`reject`。

简单的说一下Promise的几个特点。

== 未完待续 ==

<!-- more -->