---
title: IE11 Get请求缓存问题
date: 2017-08-09 18:17:41
tags: IE
categories: IE
---
# 问题
最近遇到一个问题，在版本是11.0.9600.18665的IE11上，发送给后台的get请求总是返回同样的值。给后台发送GET请求主要是获取验证码，但是比较神奇的是打开F12后就会发送GET请求了。

<!-- more -->
# 分析
首先，我在页面里面打印一下每次GET请求返回的值，如下：

```

$.get('/login/securitycode1' , function(result){
    alert(result)//为什么要用alert，因为在不打开F12的情况下console.log无法看到，因为打开F12后GET请求就正常了
})

```
然后在服务器那块打印一下是否接受到了请求

```
router.get('/securitycode', function(req, res, next) {
	console.log("receive get request");
	// ...
});
```
最后发现页面里面alert的值每次都是一样，而服务器根本就没有收到页面发来的请求，也就是说nodejs根本没打印出来"receive get request"。
# 结论
我觉得是IE11在发现有相同的GET请求后，就会在浏览器缓存里面找，而不会发送请求，打开F12就变成了开发者模式，这时候所有请求都会发送（猜的）。上面的观点还没找资料验证过，都是个人YY。
注：版本为11.0.9600.17843的IE11浏览器就没这问题。。。绝了。。。


