---
title: nodejs搭建接口服务器和mysql增删改查
date: 2019-01-11 16:34:27
tags: Nodejs
categories: Nodejs
---

# 准备工作
1. [下载Postman和Navicat并安装](https://pan.baidu.com/s/17hRqsMZB1ehMXTvmAsvz2w)

    提取码：**9knh**

    **postman不需要注册**
    
    **navicat有个破解补丁，把简体中文64位里面的两个文件拷贝到安装目录下即可。每次打开点击跳过（如下图）**

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