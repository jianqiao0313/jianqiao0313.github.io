---
title: linux开启启动nodejs服务
date: 2018-03-06 15:59:58
tags: Nodejs
categories: Nodejs
---
# 需求
linux服务器开机自启动nodejs。
<!-- more -->
# 方法
## 首先安装forever来守护进程

```
npm install forever -g
```
## 然后安装forever-service来自动生成/etc/init.d下的service

```
npm install forever-service -g
```
## 然后注册服务
forever-service他会自动寻找目录下的app.js，所以直接在项目目录下运行

```
forever-service install myService
```
现在切换到/etc/init.d/这个目录，可以看到有个新的文件叫myService，用service myService start即可启动

## 最后修改开机启动项
在/etc/rc.local里面添加一句即可

```
service myService start
```
## 注

### 启动、停止、重启服务命令
```
// 启动 停止 重启服务的命令
service myService start
service myService stop
service myService restart
```
### 不是app.js作为项目启动文件

```
// 如果你的项目不是以app.js作为启动文件的，比如你是以index.js作为启动文件的，那么如何生成服务呢？
forever-service install myService --script index.js
```
### 如果forever不是全局安装的话
```
// 如果forever不是全局安装的话，生成service的时候会报错，提示forever not found。这时候运行下面的命令指定forever路径即可
forever-service install myService -p /usr/local/bin/forever

```
### 如果启动脚本需要传值
```
// 如果启动脚本有一些参数的话，调用如下
forever-service install myService -o " param1 param2"
```
# 尝试过的方法
## 用&来启动
在rc.local里面写入下面这行代码:
```
node /usr/local/hello/app.js &
```
结果并没有运行，后来我在根目录运行这行代码，提示找不到依赖包，于是修改rc.local如下
```
cd /usr/local/hello/
node app.js &
```
结果并没有启动，用ps -ef命令查看进程，并没有找到nodejs
## 用forever启动
在rc.local里面写入下面这行代码：
```
cd /usr/local/hello/
forever start app.js
```
结果也是没有启动
## 用写好forever的shell脚本来启动
```
// a.sh
#! bin/sh
cd /usr/local/hello
forever start app.js
```
还是不行。。。
## 总结一下失败的方法
应该看下linux系统的启动日志，找出出错原因。

# 参考文章
[Node.js script at startup](https://causeyourestuck.io/2016/04/30/run-node-js-script-startup/)

[Github - forever-service](https://github.com/zapty/forever-service)

[Github - forever](https://github.com/foreverjs/forever)





