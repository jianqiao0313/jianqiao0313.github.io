---
title: 前后端分离 Nodejs作为Webserver 转发请求给Tomcat
date: 2017-04-25 17:04:12
tags: Nodejs
categories: Nodejs
---
# 项目环境
我前端框架用的是Angular 1.5.8，Nodejs + express。后台用的是Tomcat，提供接口。

所有通讯都是通过get、post请求的方式。

# 问题
遇到的很大的一个坑：如何判断用户是否登录？

# 解决方法
有两种方式判断用户是否登录：

一、设置请求的cookie里面的JSESSIONID 

二、跟后台沟通好，设置一个token，每次请求都发送token

我们项目的流程是先登录。首先发送个get请求获取验证码，这时候后台返回一个JsessionId，然后我的每次get、post请求都需要在cookie里面加上JsessionId。

先贴张直接访问Tomcat服务器验证码接口的get请求的图：

![image](http://oop1po68r.bkt.clouddn.com/nodejs-1.png)
在Request Headers里面的cookie ： JSESSIONID  这个就是后台返回的。询问了一下后台人员，jsessionid这是请求成功后Tomcat自动加的。

下面问题来了，如何拿到JSESSIONID，然后重新组装cookie给后台发回去呢？

我没用中间件来实现，直接用的原生http模块，下面上代码：

```
//路由文件 login.js
router.get('/securitycode1', function(req, res, next) {
	var options = { 
	    hostname: global._hostname,  // 主机名
	    path: global._path+'login/securitycode1',  //访问的路径
	    method: 'GET', 
	};
	var results = ''; 

	var reqq = http.request(options, function(ress) {
	    ress.on('data', function (chunk) {
	        results = results + chunk;
	    }); 
	    
	    ress.on('end', function () {
	        global.jsessionid = ress.headers['set-cookie'][0].match(/JSESSIONID=(\S*);/)[1];
	        //通过ress的头部拿到JSESSIONID这行字符串，用正则匹配出来 存到global里面。
	        
	        console.log("JSESSIONID: "+global.jsessionid);

	        res.send(results);//把Tomcat发送的请求转发回客户端（即Angular）
	    }); 
	});

	reqq.on('error', function(e) {
	        //TODO
	});
	reqq.end(function(){

	});
});
```
现在，nodejs所有页面都可以用JSESSIONID了，使用方法：global.jsessionid。

# 举个使用的例子

```
//路由文件 login.js
router.post('/' , function(req , res , next){
	var data = require('querystring').stringify(req.body);//这是post方法提交的数据（form表单序列化）
	var options = { 
	    hostname: global._hostname,
	    path: global._path+'login',
	    method: 'POST',
	    headers: {'Cookie': 'JSESSIONID='+global.jsessionid ,"Content-Type": 'application/x-www-form-urlencoded', "Content-Length": data.length   }
	    //通过headers Cookie直接设置JSESSIONID
	};
	console.log(options);
	var results = ''; 

	var reqq = http.request(options, function(ress) {
	    ress.on('data', function (chunk) {
	        results = results + chunk;
	    }); 
	    
	    ress.on('end', function () {
	        res.send(results);//把Tomcat发送的请求转发回客户端（即Angular）
	    }); 
	});

	reqq.on('error', function(e) {
	});
	reqq.write(data); //把数据写到请求里面
	reqq.end(function(){
	});

});
```
这样所有的请求就都有了JSESSIONID，登陆后就可以访问其他的Tomcat接口了。

好像express-http-proxy这个express中间件也可以实现，但是没细研究。

前后端分离还在摸索中，最近得好好拜读一下老外的文章了。

# 尾巴

推荐两篇文章（得翻墙）：

> [Cookies vs Tokens. Getting auth right with Angular.JS](https://auth0.com/blog/angularjs-authentication-with-cookies-vs-token/)

> [The Ins and Outs of Token Based Authentication](https://scotch.io/tutorials/the-ins-and-outs-of-token-based-authentication)

这两篇都是讲的前后端分离，很有启发意义。