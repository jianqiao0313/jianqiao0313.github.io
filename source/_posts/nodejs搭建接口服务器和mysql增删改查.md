---
title: nodejs搭建接口服务器和mysql增删改查
date: 2019-01-11 16:34:27
tags: Nodejs
categories: Nodejs
---
# 简介

从零单排学会用nodejs搭建接口服务器，对mysql进行增删改查

<!-- more -->

# 准备工作
1. [下载Postman和Navicat并安装](https://pan.baidu.com/s/17hRqsMZB1ehMXTvmAsvz2w)

    提取码：**9knh**

    **postman不需要注册**
    
    **navicat有个破解补丁，把简体中文64位里面的两个文件拷贝到安装目录下即可。每次打开点击试用（如下图）**

    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-2.png)

2. 查看本地Nodejs版本（运行命令 **node -v** ）

    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-1.png)
   
    只要保证Nodejs版本为8即可

# 用navicat连接阿里云数据库，并创建一个表

1. 打开软件 -> 文件 -> 新建连接 -> Mysql（如下图）
   
    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-3.png)

2. 填写Mysql信息（如下图）

    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-4.png)
    
    > 密码我单独给你

3. 点击 **测试连接**，弹出 **连接成功**（如下图）

    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-5.png)

4. 点击 **确定**后，左侧出来一个阿里云的链接（如下图）
   
    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-6.png)

5. 双击 **阿里云** , 左边有个 **gzcs** , 这个数据库就是我们要测试用的（如下图）
   
    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-7.png)

6. 点击 **创建表** （如下图）

    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-8.png)

7. 输入名为 **Id**，长度 **10**，**不为null**，单击 **键**，**自动递增** （如下图）

    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-9.png)

8. 点击 **添加字段**，输入名为 **name**，字符集为 **utf8** （如下图）
   
    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-10.png)

9. 点击保存，输入表名 **test** （如下图）

    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-11.png)

10. 左侧应该有 **test** 这个表了，双击（如下图）

    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-12.png)

11. 猛点最下面的 **+** , 咱创建几条数据（如下图）

    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-13.png)

12. 五条记录已经建好了，下面给这五条记录的 **name** 字段赋值，内容随便填，点击 **√** （如下图）

    ![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-14.png)

13. 大功告成，现在数据库已经准备好了。

# 安装express，搭建接口服务器

1.  安装express应用生成器

```
npm install express-generator -g
```

2. 创建一个叫 **gzcs** 的应用

```
express gzcs
```
![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-15.png)

3. 打开目录应该长这样

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-16.png)

4. 安装依赖
```
npm install
```
![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-17.png)

5. 运行应用
```
npm start
```
![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-18.png)

6. 打开浏览器访问 **http://localhost:3000**

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-19.png)

# express里面增加get、post请求

1. 打开 **routes/index.js**

2. 增加如下代码
```
// 增加一个路径为a的GET路由，返回一个json
router.get('/a', function (req, res, next) {
  res.send({
    success: 1,
    method: 'GET'
  });
});
// 增加一个路径为a的POST路由，返回一个json
router.post('/a', function (req, res, next) {
  res.send({
    success: 1,
    method: 'POST'
  });
});
```
3. 整体的index.js长这样
```
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function (req, res, next) {
  res.render('index', {
    title: 'Express'
  });
});
// 增加一个路径为a的GET路由，返回一个json
router.get('/a', function (req, res, next) {
  res.send({
    success: 1,
    method: 'GET'
  });
});
// 增加一个路径为a的POST路由，返回一个json
router.post('/a', function (req, res, next) {
  res.send({
    success: 1,
    method: 'POST'
  });
});

module.exports = router;
```
4. 重启服务
```
按 ctrl + C
npm start
```

# 用postman测试新增的接口

1. 打开 **postman**， 输入 **http://localhost:3000/a** ，点击 **send** （如下图）

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-20.png)

2. 点击 **+** ， 把方法改为 **post**， 点击 **send** （如下图）

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-21.png)

可以看到，刚才我们在 **index.js** 的路由文件里面加的两个接口已经成功了，可以监听 **/a** 的 **get** 请求和 **/a** 的 **post** 请求。

# 提升幸福感的小插件-nodemon
目前我们每次更新代码，都需要关闭服务后再执行 **npm start**，这样太麻烦了，可以全局安装 **nodemon**，并用 **nodemon** 启动服务，这样每次更新代码后，服务就会**自动重启**，很方便。

1. 全局安装 **nodemon**

```
npm install nodemon -g
```

2. 用 **nodemon** 启动服务

```
nodemon bin/www
```
![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-22.png)

# 用nodejs操纵mysql