---
title: future-blog
date: 2017-10-20 09:12:58
tags:
top_img: https://gezichenshan.top/cms/bg
---
# 添加git私钥
添加私钥是因为可以用ssh方式使用git，而另一种http方式则必须输入用户名密码，较麻烦。

## 步骤

### 1、 在桌面右键 -> Git Bash

### 2、 告诉git你的名字，这个名字会出现在你的提交记录中
  ```
  git config --global user.name "你的名字"
  // 如 git config --global user.name "mmml"
  ```

### 3、 告诉git你的邮箱，这个Email也会出现在你的提交记录中
```
git config --global user.email "你的Email"
// 如 git config --global user.email "abc@abc.abc"
```

### 4、 生成SSH key（私钥）
```
ssh-keygen -t rsa -C "你的Email"
// 如 ssh-keygen -t rsa -C "abc@abc.abc"
// 运行完这条命令后控制台会出现如下内容，如下图
```
![image](https://static.gezichenshan.top/blog/blog-1-1.png)
提示信息的意思是：是否要保存在c:\user\Administrator\.ssh\目录下

这时候按回车，然后如下图                  
![image](https://static.gezichenshan.top/blog/blog-1-2.png)

提示信息的意思是：是否需要密码

直接按回车(代表不用密码)

然后再按回车

出现下图代表私钥生成成功                        
![image](https://static.gezichenshan.top/blog/blog-1-3.png)

### 5、 打开我的电脑 -> c盘 -> 用户 -> "你当前的用户"(如 我的就是Administrator) -> .ssh文件夹 -> 可以看到有两个文件，如下图             
![image](https://static.gezichenshan.top/blog/blog-1-4.png)

### 6、 用记事本打开id_rsa.pub(sublime webstorm都可以)

### 7、 把里面所有的内容复制下，如下图                 
![image](https://static.gezichenshan.top/blog/blog-1-5.png)

### 8、 打开github的网页，登录后，在右上角点击setting，如下图
![image](https://static.gezichenshan.top/blog/blog-1-6.png)

### 9、 然后点击左侧的 *SSH and GPG keys* -> 点击 右上角的 *New SSH key*，如下图
![image](https://static.gezichenshan.top/blog/blog-1-7.png)

### 10、 把刚才复制的字符串复制到 *Key* 里，*Title* 随便填，代表这个SSH Key的名字，然后点击 *Add SSH Key* 如下图
![image](https://static.gezichenshan.top/blog/blog-1-8.png)

### 11、 可能会让你输入密码

### 12、 出现下图代表成功                     
![image](https://static.gezichenshan.top/blog/blog-1-9.png)

# 创建Git仓库，并把Vue项目添加到仓库里

## 步骤

### 1、 点击右上角的 *New repository*，如下图              
![image](https://static.gezichenshan.top/blog/blog-1-10.png)

### 2、 输入 *Repository name*（仓库名称） 并且把 * Initialize this repository with a README * 打钩（创建项目的时候建立README， 因为git仓库不能有空文件夹，必须得有个文件）。
![image](https://static.gezichenshan.top/blog/blog-1-12.png)

### 3、 出现下图代表创建成功
![image](https://static.gezichenshan.top/blog/blog-1-13.png)

### 4、 点击右边的*Clone or download*按钮，复制ssh地址如下图
![image](https://static.gezichenshan.top/blog/blog-1-14.png)
> 注：咱们用的是ssh的方式上传下载代码，如果需要用http的方式（就是每次都输入用户名密码） 可以点一下 *Use HTTPS*

### 5、 在你想创建项目的文件夹里右键 -> Git Bash。比如我想在桌面创建，就在桌面右键 -> Git Bash

### 6、 在Bash里输入*git clone git@github.com:jianqiao0313/futureblog.git*。clone 后面的仓库地址就是第4步你复制的地址。
> 在Git Bash里面黏贴有两种方式，第一种是在终端里右键 ->  Paste，如下图。第二种是 *shift + Ins* 。
![image](https://static.gezichenshan.top/blog/blog-1-15.png)

### 7、 第一次clone仓库会问你是否继续连接，输入 *yes* 按回车。如下图
![image](https://static.gezichenshan.top/blog/blog-1-17.png)

### 8、 出现下图代表成功                           
![image](https://static.gezichenshan.top/blog/blog-1-18.png)

### 9、 打开本地Futureblog的文件夹，如 我在桌面右键 -> Git bash ->git clone的，路径就是C:\Users\Administrator\Desktop\futureblog，如下图
![image](https://static.gezichenshan.top/blog/blog-1-19.png)

### 10、 新建两个文件夹，一个叫front，一个叫end。front是前端的代码，end是后台的代码，我来维护end文件夹，你来维护front文件夹。建好后在end文件夹随便建个文件，如 *新建文本文档.txt*。创建这个文件是因为git不让传空文件夹。如下图          
![image](https://static.gezichenshan.top/blog/blog-1-20.png)
![image](https://static.gezichenshan.top/blog/blog-1-21.png)

### 11、 把vue文件夹my-project复制到front文件夹下，如下图
![image](https://static.gezichenshan.top/blog/blog-1-22.png)

> 注：不用添加.gitignore文件来忽略，因为vue已经建好了.gitignore文件，如下图
![image](https://static.gezichenshan.top/blog/blog-1-24.png)

### 12、 把代码上传到github，在futureblog这个文件夹右键 -> Git Bash
![image](https://static.gezichenshan.top/blog/blog-1-23.png)

### 13、 输入以下命令

```
git add .
git commit -am '初始化项目'
git push
```
- git add . 
 
    代表的是 把新增的文件提交到预提交区
- git commit -am '初始化项目'

    表示提交一个commit（注 commit是git提交的单位），-a指的是把刚才咱们add .的文件都塞到commit里，-m指的是后边单引号里的是对这次commit的说明（注 git的commit 必须有-m参数，必须有说明，也就是'初始化项目',当然引号里面的内容随意填就好了，如 git commit -am '啊加加啊加'）
- git push
    push到git仓库里去

执行完如下图的话就代表成功了
![image](https://static.gezichenshan.top/blog/blog-1-25.png)

### 14、 我们去github上看眼，是否有这次记录。大功告成！如下图
![image](https://static.gezichenshan.top/blog/blog-1-26.png)

# 把我添加到你的项目组里，这样我才能维护你的仓库的代码
### 如下图，然后点击 *Add collaborator*
![image](https://static.gezichenshan.top/blog/blog-1-27.png)
