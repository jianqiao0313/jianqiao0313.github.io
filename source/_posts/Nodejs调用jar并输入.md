---
title: Nodejs调用jar并输入
date: 2017-08-30 16:44:10
tags: Nodejs
categories: Nodejs
---
# 需求
调用.jar文件，并且模仿bash输入，如下图
<!-- more -->
![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodejs-4-1.png)
模仿cmd输入java -jar testKeyAuth.jar，并且输入参数，如3。
# 问题
- 无法通过'java -jar testKeyAuth.jar 3'这条命令来输入第一个参数，如下图
![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodejs-4-2.png)
我在后面输入3，这个输入项并没有在程序中体现
- nodejs如何执行java -jar命令
# 如何粗暴的解决问题
![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodejs-4-3.png)
照着文档上的抄

```
const { spawn } = require('child_process');
const bat = spawn('java', ['-jar', 'testKeyAuth.jar']);

bat.stdout.on('data', (data) => {
	out.push(data.toString());
});

bat.stderr.on('data', (data) => {
	console.log(data.toString());
});

bat.on('exit', (code) => {
  console.log(`子进程退出码：${code}`);
});
```
通过spawn来运行命令，那么问题来了，如何输入呢？

简单粗暴的方法就是通过stdin.write这个流来进行写。如下代码：

```
setTimeout(function(){
    bat.stdin.write('3\n');
},200);
setTimeout(function(){
    bat.stdin.write('\n');
},400);
```
这代码的意思就是200毫秒后输入3回车，400毫秒后输入回车。
# 总结
没找到更好的方式来优雅的输入参数，讲道理应该修改jar包才对，然后通过'java -jar testKeyAuth.jar 3'这种方式输入，但是没有源代码，所以无法修改，只能用定时器的方式模拟，nodejs的写流还挺方便的。

