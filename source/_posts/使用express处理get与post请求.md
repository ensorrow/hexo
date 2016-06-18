title: 使用express处理get与post请求
date: 2016-05-23 10:41:53
tags: [Node, Express]
---

写网站后端的入门需求必然是处理get请求和post请求，使用express处理这两种请求都特别的简单，一眼就能看懂。

首先是get请求，get请求的参数都附在请求的url后面（即Query String），要获取一个请求然后读取它的Query就像这样：

	//前端
	GET /test?name=lzy&netid=21232

	//后端,app是一个express实例
	app.get('/tset', function(req, res){
		console.log(req.query.name);
		console.log(req.query.netid);
		//req即request对象
		})

然后就是POST请求，post请求的请求内容在body里，需要使用bodyParser中间件（EUX项目已引入）；
	
	//前端
	<form action="/test" method="post">
		<input type="text" name="username">
		<input type="text" name="address">
	</form>
	//后端
	app.post('/test', function(req, res){
		console.log(req.body.username);
		console.log(req.body.address);
		//使用了bodyParser中间件
		})

当然啦，如果你请求的url后面加上Query string`form action="/test?id=xxx"`你也可以在req对象的query属性获取到它。

get请求除了query string的形式，还可以通过请求路径来传递信息，举个栗子：

	GET /test/lzy/21415

	app.get('/test/:name/:netid', function(req, res){
		console.log(req.params.name);
		console.log(req.params.netid);
	})

把http请求结合一下ajax，就是这个样子了：

	//前端
	//ajax验证用户名是否存在
    var $useridInput = $('input[name=userid]');
    $useridInput.change(function() {
        var tmpurl = '/user/chkuser?userid='+$useridInput.val();
        $.ajax({
            url: tmpurl,
            success: function(data,status){
                if(data.exist){
                    console.log('用户名存在!');
                } else{
                    console.log('用户名不存在');
                }
            },
            dataType: 'json'
        });
    });

    //后端
    router.get('/chkuser', ajaxchk);
	function ajaxchk(req, res){
	    var inuser = req.query.userid;
	    var User = mongoose.model('User');
	    User.findOne({userid:inuser}, function(err, result){
	        if (err) console.log(err);
	        console.log(result);
	        if(result){
	            console.log('en')
	            return res.json({exist: 1});
	        } else {
	            return res.json({exist: 0});
	        }
	    });
	}

这样差不多就能做一些简单的功能啦😅