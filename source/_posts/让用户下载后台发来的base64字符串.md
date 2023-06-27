---
title: 让用户下载后台发来的base64字符串
date: 2017-12-05 10:22:40
tags: JS 
categories: JS
top_img: https://gezichenshan.top/cms/bg
---
# 需求
后台发来一串base64编码的字符串，是一个xxx.doc文件，现在想让用户点击下载按钮下载。

base64字符串如下：

```
// 截取了一部分
0M8R4KGxGuEAAAAAAAAAAAAAAAAAAAAAPgADAP7/CQAGAAAAAA.............AAAAAAAAAAAAAAAAAAAAAAAAAAAA=
```
# 下载方法
很简单，在页面添加一个A标签或者渲染dom的时候直接渲染出来一个下载的A标签就行了。A标签如下：
```
<a download="jianqiao0313.doc" href="data:application/msword;charset=utf-8;base64,0M8R4KGxGuEAAAAAAAAAAAAAAAAAAAAAPgADAP7/CQAGAAAAAA.............AAAAAAAAAAAAAAAAAAAAAAAAAAAA=">下载</a>
```
注意要更改一下 **data** 的格式，后台给我发过来的是doc文件，所以 **data** 就是 **application/msword** 。

# 尝试过的方法
## 把base64解码后通过blob下载
代码如下：

```
var base64Str = '0M8R4KGxGuEAAAAAAAAAAAAAAAAAAAAAPgADAP7/CQAGAAAAAA.............AAAAAAAAAAAAAAAAAAAAAAAAAAAA='
var blob = new Blob([atob(base64Str)],{type:'application/msword'});

var a = document.createElement("a");
a.href = window.URL.createObjectURL(blob);
a.download = "helloword";
a.textContent = "Download Hello World!";

document.body.appendChild(a);
```
这样运行后发现，下载下来的doc是乱码，我开始以为是中文解码的问题，搜了下需要用  **decodeURIComponent(window.atob('base64Str'))** 来进行解码，但是我一运行就报错： 
```
VM1881:1 Uncaught URIError: URI malformed
    at decodeURIComponent (<anonymous>)
    at <anonymous>:1:2
```
没细琢磨这个报错，谷歌搜了一下，感觉像是 **%** 的问题。

于是换了一个解码的方式：[webtoolkit.base64.js](http://www.webtoolkit.info/javascript-base64.html#.WiYIQ_mWaM9)。人家写好了一个类叫Base64，有两个方法encode、decode。直接Base64.encode('中文')就可以了编码中文了，用Base64.decode('xxxxxx')就可以了解码刚才的中文编码了。很方便。于是更改刚才的代码如下:
```
var base64Str = '0M8R4KGxGuEAAAAAAAAAAAAAAAAAAAAAPgADAP7/CQAGAAAAAA.............AAAAAAAAAAAAAAAAAAAAAAAAAAAA='
var blob = new Blob([Base64.decode(base64Str)],{type:'application/msword', encoding: 'utf-8'});

var a = document.createElement("a");
a.href = window.URL.createObjectURL(blob);
a.download = "helloword";
a.textContent = "Download Hello World!";

document.body.appendChild(a);
```
发现下载下来的还是乱码，只不过乱码长的稍微不一致。

到现在我还是怀疑乱码的问题，于是自己写了个input，想看看到底是不是乱码的问题，页面如下：

```
<input type="file" id="aaa">
```
然后选择一个文件后运行如下js代码:
```
// 拿到文件
var file = document.getElementById('aaa').files[0];
```
拿到之后用 **FileReader** 来读取内容。
```
var res = '';
var reader = new FileReader();
reader.onload = function(e) {
  res = e.target.result;
}
reader.readAsBinaryString(file);
```
读取完了后，用 **btoa** 来编码刚才的 **res** 。
```
btoa(res);
```
然后我发现居然跟后台返回的一毛一样！终于知道不是编码的问题了，接着去谷歌搜，看到Stack Overflow有人给出了文章开头那样的方法下载，尝试后终于成功了。看来还得好好学学blob、file。

# 参考的网页
> [文件和二进制数据的操作](http://javascript.ruanyifeng.com/htmlapi/file.html#toc0)

> [Create a file in memory for user to download, not through server](https://stackoverflow.com/questions/3665115/create-a-file-in-memory-for-user-to-download-not-through-server)

> [解决 Javascript 中 atob 方法解码中文字符乱码问题](https://blog.coding.net/blog/resolve-atob-decode-chinese-character-outputting-messy-code-problem-in-javascript)

> [Base64 encode/decode and download content generated in URL](https://stackoverflow.com/questions/10755749/base64-encode-decode-and-download-content-generated-in-url)

> [How to convert file to base64 in JavaScript?](https://stackoverflow.com/questions/36280818/how-to-convert-file-to-base64-in-javascript)

> [Convert base64 png data to javascript file objects](https://stackoverflow.com/questions/16968945/convert-base64-png-data-to-javascript-file-objects)


