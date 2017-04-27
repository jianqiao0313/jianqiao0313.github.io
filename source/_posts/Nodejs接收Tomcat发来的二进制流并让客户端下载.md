---
title: Nodejs接收Tomcat发来的二进制流并让客户端下载
date: 2017-04-27 16:48:07
tags: Nodejs
categories: Nodejs
---
# 环境
前端是Angular 1.5.8 ， nodeJs + express。Nodejs作为代理服务器，转发客户端的Get、Post请求给Tomcat，并把接受到的Tomcat请求返回给客户端。客户端指的就是浏览器。

后台是Tomcat。提供接口。

# 需求
后台Tomcat返回一个excel的2进制流，Nodejs接收到这个2进制流后返回给客户端。

本文的2进制流传送的是excel表格，换成其他文件也同理，反正都是2进制嘛~

# 问题
客户端接收到的Excel是乱码，测试了各种编码（binary、utf8、gbk等），都是乱码。

# 关于问题的思考
其实我饶了一圈，2进制流就是2进制流，不用考虑编码。

# 代码

```
//Express 路由的js
router.get(/^\/(\d+)\/print/ , function(req , res , next){
    var uid = req.params[0]; //正则匹配的\d
    var name = req.query.name; //url传过来的excel的名字
    name += '的申请表';
    var options = { 
            hostname: global._hostname,
            path: global._path+'application/'+uid+'/print',
            method: 'GET',
            headers: {'Cookie': 'JSESSIONID='+global.jsessionid }//请求增加JSESSIONID，后台可以凭借这个判断用户是否登录
        };
        var chunks = [];//返回的2进制流，需要用数组接收
        var size = 0; //2进制流的size

        var reqq = http.request(options, function(ress) {
            ress.on('data', function (chunk) {
                console.log(chunk);
                chunks.push(chunk);//把二进制流存到chunk这个数组里
                size += chunk.length;//size增加
            }); 
            ress.on('end', function () {
                res.setHeader("Content-Type", "application/vnd.ms-excel");//把header改成xls
                res.setHeader("Content-Disposition","attachment;filename="+encodeURI(name)+".xls");//把名字改成url传过来的名字，注意要用encodeURI编码一下，否则报错
                res.setHeader("Expires","0");
                res.setHeader("Pragma","no-cache");

                var buffer = Buffer.concat(chunks , size);//调用buffer的concat连接2进制流

                res.send(buffer);//返回2进制流，不用调用toString方法
            }); 
        });

        reqq.on('error', function(e) {
        });
        reqq.end(function(){
        });
});
```
## 截图
### 后台返回的2进制流（chunks数组里面的元素）
![image](http://oop1po68r.bkt.clouddn.com/Nodejs-2-1.png)
可能跟Nodejs的设计有关，接收到的2进制流不是一次接收完，一共接收了6次才接收完，我对Nodejs的原理还不是很了解，所以无法解释为什么要打印6次。


# 走过的弯路
## 之前一直以为是编码的问题，尝试的代码如下

```
//更改buffer的编码
var buf = Buffer.concat(chunks , size);
var str = iconv.decode(buf , 'utf8');//iconv是一个字符转码的工具
var str = iconv.decode(buf , 'binary');
var str = iconv.decode(buf , 'gb2312');
var str = iconv.decode(buf , 'gbk');
res.send(str);
```
浏览器接收到这些str后，保存成xxxx.xls，用office打开后全是乱码，因为根本就不是编码的问题嘛！

## 直接把后台返回的chunk返回给浏览器，尝试的代码如下

```
//Express 路由的js
router.get(/^\/(\d+)\/print/ , function(req , res , next){
    var uid = req.params[0];
    var options = { 
            hostname: '10.120.12.125',
            path: '/mcms/v1/application/'+uid+'/print',
            method: 'GET',
            headers: {'Cookie': 'JSESSIONID='+global.jsessionid }
        };
        var results = ''; 

        var reqq = http.request(options, function(ress) {
            ress.on('data', function (chunk) {
                results = results + chunk;//简单粗暴地把chunk当字符串连接到一起
            }); 
            ress.on('end', function () {
                res.send(results);//把连接好的chunk字符串返回给浏览器
            }); 
        });

        reqq.on('error', function(e) {
        });
        reqq.end(function(){
        });
});
```
结果当然是乱码了，因为没用人家的Buffer.concat方法，字符串的+好像不能直接用在Buffer上。


## iconv工具的地址（虽然没用上，但还是附一下吧）
   [Github](https://github.com/ashtuchkin/iconv-lite)

   [Npm](https://www.npmjs.com/package/iconv-lite)
   
# 结果
浏览器成功下载了excel，并且下载的excel名字为中文的。

截个图庆祝一下

![image](http://oop1po68r.bkt.clouddn.com/Nodejs-2-2.png)