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

1. 安装mysql的npm包

```
npm install mysql --save
```
![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-23.png)

2. 在项目根目录创建config文件夹，在config文件夹里创建db.js

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-24.png)

3. db.js里填入以下内容

> **密码我单独给你**
```
// gzcs就是我们的这次测试的数据库名
var mysql = require("mysql");
var pool = mysql.createPool({
    host:"47.95.2.204",
    user:"root",
    password:"",
    database:"gzcs"
});

function query(sql,callback){
    pool.getConnection(function(err,connection){
        connection.query(sql, function (err,rows) {
            callback(err,rows);
            connection.release();
        });
    });
}

exports.query = query;
```

4. 在routes/index.js里查询test表里所有数据 
```
var express = require('express');
var router = express.Router();
var db = require("../config/db");
/* GET home page. */
router.get('/', function (req, res, next) {
  res.render('index', {
    title: 'Express'
  });
});
// 增加一个路径为get的GET路由，返回一个json
router.get('/get', function (req, res, next) {
  db.query('select * from test', function (err, rows) {
    if (err) {
      res.send({
        success: 0,
        message: '数据库报错,' + err
      });
    } else {
      res.send({
        success: 1,
        data: rows,
        message: '查询test表成功'
      })
    }
  })
});

module.exports = router;
```
5. 在postman里测试咱们写的接口对不对

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-25.png)

6. 插入数据

```
// insert
router.post('/insert', function (req, res, next) {
  let name = req.body.name;
  db.query(`insert into test (name) values ('${name}')`, function (err, rows) {
    if (err) {
      res.send({
        success: 0,
        message: '新增失败'
      });
    } else {
      res.send({
        success: 1,
        message: '新增成功'
      })
    }
  });
})
```
> 注：插入的数据字段有多个的时候这么写db.query(`insert into test (name,age) values ('${name}', ${age})`, function(err, rows){});

7. 在postman里测试插入接口

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-26.png)

> 注：因为id为自增，所以不需要传入

8. 删除数据
```
// delete
router.post('/delete', function (req, res, next) {
  let id = req.body.id;
  db.query(`delete from test where id=${id}`, function (err, rows) {
    if (err) {
      res.send({
        success: 0,
        message: '删除失败'
      });
    } else {
      res.send({
        success: 1,
        message: '删除成功'
      })
    }
  });
})
```
> 注 因为id是int类型 所以where id=${id}不需要加引号，如果想通过name来删除，需要这么写db.query(`delete from test where name='${name}'`, function(err,row){});

9. 在postman里测试删除接口

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-27.png)

已经删除了第6条记录

10. 修改数据
```
// update
router.post('/update', function (req, res, next) {
  let id = req.body.id;
  let name = req.body.name;
  db.query(`update test set name='${name}' where id=${id}`, function (err, rows) {
    if (err) {
      res.send({
        success: 0,
        message: '修改失败'
      });
      console.log(err);
    } else {
      res.send({
        success: 1,
        message: '修改成功'
      })
    }
  });
});
```
> 注：更新一条记录多个字段的时候这么写db.query(`update test set name='${name}',age=${age} where id=${id}`, function(err, rows){});

11. 在postman里测试修改接口

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-28.png)

已经把第五条记录的 **heihei** 改成了 **jiao**

# 小结

目前你已经学会了最基本的增删改查，我把 **index.js** 的代码都贴出来，接下来会学习添加多条数据，插入多条数据，修改多条数据，删除多条数据，给表做分页，根据字段来排序。

```
var express = require('express');
var router = express.Router();
var db = require("../config/db");
/* GET home page. */
router.get('/', function (req, res, next) {
  res.render('index', {
    title: 'Express'
  });
});
// select
router.get('/get', function (req, res, next) {
  db.query('select * from test', function (err, rows) {
    if (err) {
      res.send({
        success: 0,
        message: '数据库报错,' + err
      });
    } else {
      res.send({
        success: 1,
        data: rows,
        message: '查询test表成功'
      });
    }
  });
});
// insert
router.post('/insert', function (req, res, next) {
  let name = req.body.name;
  db.query(`insert into test (name) values ('${name}')`, function (err, rows) {
    if (err) {
      res.send({
        success: 0,
        message: '新增失败'
      });
    } else {
      res.send({
        success: 1,
        message: '新增成功'
      })
    }
  });
})
// delete
router.post('/delete', function (req, res, next) {
  let id = req.body.id;
  db.query(`delete from test where id=${id}`, function (err, rows) {
    if (err) {
      res.send({
        success: 0,
        message: '删除失败'
      });
    } else {
      res.send({
        success: 1,
        message: '删除成功'
      })
    }
  });
})
// update
router.post('/update', function (req, res, next) {
  let id = req.body.id;
  let name = req.body.name;
  db.query(`update test set name='${name}' where id=${id}`, function (err, rows) {
    if (err) {
      res.send({
        success: 0,
        message: '修改失败'
      });
    } else {
      res.send({
        success: 1,
        message: '修改成功'
      })
    }
  });
});
module.exports = router;
```
# 高级用法

1. 首先我们把表弄的复杂一点

打开navicat，右键点击 **设计表** ，如下图

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-29.png)

2. 新增 **age** 字段，点击 **添加字段** ，输入 **age** ，类型是 **Int**， 长度是 **3**（因为没有超过999岁的人），点击 **保存** 。如下图

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-30.png)

3. 刷新数据库，就能看到新增的列了，如下图

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-31.png)

4. 我们给随便添加点数据，然后点击 **√** ，如下图

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-32.png)

5. 修改一下多条数据操纵的接口，打开 **config/db.js**
```
var mysql = require("mysql");
var pool = mysql.createPool({
    host: "47.95.2.204",
    user: "root",
    password: "", // 密码我单独给你
    database: "gzcs"
});

function query(sql, callback) {
    pool.getConnection(function (err, connection) {
        connection.query(sql, function (err, rows) {
            callback(err, rows);
            connection.release();
        });
    });
}
// 新增多条操作的接口
function multipleQuery(sql, values, callback) {
    pool.getConnection(function (err, connection) {
        connection.query(sql, values, function (err, rows) {
            callback(err, rows);
            connection.release();
        });
    });
}
exports.query = query;
exports.multipleQuery = multipleQuery;
```

6. 在 **router/index.js** 里添加 **插入多条** 的方法

```
// insert multiple
router.post('/insertMultiple', function (req, res, next) {
  // 请求带过来的数组
  let reqArr = req.body.data;
  // 我们组装后的数组
  let insertArr = [];
  // 组装数据
  reqArr.forEach(function (item) {
    insertArr.push([item.name, item.age]);
  })
  // insertArr长这样：[['33', 33], ['44', 44]] 对应下面的sql语句中的 (name,age)。换句话说 就是把 ? 替换成inserArr
  db.multipleQuery(`insert into test (name,age) values ?`, [insertArr], function (err, rows) {
    if (err) {
      res.send({
        success: 0,
        message: '插入多条失败'
      });
    } else {
      res.send({
        success: 1,
        message: '插入多条成功'
      })
    }
  });
});
```

7. 在postman里测试插入多条接口，需要改变传值类型噢，要传json。

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-33.png)

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-34.png)

> 注：因为 **age** 是 **Int** 类型，所以不需要加 **引号**。

8. 在navicat里查看数据库结果，可以看到有了33和44。如下图

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-35.png)

9. 删除刚才创建的 **33** 和 **44**

```
// delete multiple
router.post('/deleteMultiple', function (req, res, next) {
  // 请求带过来的数组
  let reqArr = req.body.data;
  // 我们组装后的数组
  let deleteArr = [];
  // 组装数据
  reqArr.forEach(function (item) {
    deleteArr.push([item.id]);
  })
  // deleteArr长这样：[18, 19] 对应下面的sql语句中的 (id)
  db.multipleQuery(`delete from test where (id) in (?)`, [deleteArr], function (err, rows) {
    if (err) {
      console.log(err);
      res.send({
        success: 0,
        message: '删除多条失败'
      });
    } else {
      res.send({
        success: 1,
        message: '删除多条成功'
      })
    }
  });
});
```

10. 在postman里测试删除多条接口，可以看到33和44都没了。如下图

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-36.png)

11. 新增、修改多条记录请多次调用单条新增、修改接口

12. 把数据按age排序(desc 降序, asc 升序)
```
db.query('select * from test order by age desc', function (err, rows) {});
```

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/nodedjs-mysql-37.png)


13. 分页