---
title: OSS+CDN+GithubPages搭建自己的博客-上
date: 2019-11-27 19:50:57
tags:
---

# [demo体验](https://lvjianqiao.top)

# 准备工作

- github账号

# 步骤

1. 点击右上角，创建一个新的public仓库，仓库的名字为 **xxx.github.io** (*xxx*就是你github的名字，如下图：我的github名字叫 **jianqiao-test**，那么我仓库的名字就叫 **jianqiao-test.github.io**)，勾选initialize this repository with a README。

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/1.png)

2. 创建后，点击setting

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/2.png)

3. 往下翻，在Github Pages确认是否有 **Your site is published at https://xxx.github.io**。Source 应该默认不需要选，如果需要选择，选master分支即可。底下有个**Enforce HTTPS** 记得打钩。

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/3.1.png)

4. 稍等一分钟，然后访问 **xxx.github.io**，发现已经可以访问了，而且还有个HTTPS的锁

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/5.png)

5. 上图显示的是默认的ReadMe，现在我们添加一个index.html页面，然后再master分支提交一发，来更改咱们网站的显示。其实github pages显示的都是静态页面。

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/4.png)

6. 过两分钟，然后访问 **xxx.github.io**，发现已经变为咱们的index.html里的内容了

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/6.png)

7. 现在为了更方便的搭建咱们的博客，有两种选择，1、用三大框架生成单页面应用，然后替换index.html。2、用hexo这类已经很成熟的工具生成博客页面。本次教大家第二种，用hexo。

[hexo官网](https://hexo.io/)

```
// 安装hexo-cli
npm install hexo-cli -g
```

8. 安装cli成功后，切换到咱们项目的根目录，创建hexo分支，把原来的index.html删除

```
// 运行下面的命令 创建博客页面
hexo init ./

// 创建完了 安装项目依赖
npm install
```

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/9.png)

1. 下面咱们本地预览一波

```
// 本地预览
hexo server
```

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/10.png)



发现已经生成了静态博客了

10. 换一个更好看的主题 [hexo-theme-butterfly](https://github.com/jerryc127/hexo-theme-butterfly) 

[butterfly文档](https://jerryc.me/posts/21cfbf15/)

```
// 在根目录运行, 安装主题
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/Butterfly
```

11. 安装完后，在source创建_data文件夹，把themes/Butterfly/_config.yml复制并重命名为source/_data/butterfly.yml (这么做的意义在于以后的平滑升级，直接更新这个主题的代码即可升级主题)

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/11.png)

12. 修改根目录的_config.yml，把主题改为ButterFly

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/12.png)


13. 本地预览一波

```
hexo server
```

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/13.png)

14. 预览没问题的话，开始部署

```
// 修改根目录的_config.yml
deploy:
  type: git
  repo: git@github.com:jianqiao0313/jianqiao0313.github.io.git// 改为你的项目地址
  branch: master // master分支
```

这时候你应该还在hexo分支，修改配置的后当你运行deploy命令的时候，就会自动推到master分支了，也就是说咱们在hexo分支写文章，master分支是生成的博客页面。

```
// 发布吧~
hexo g -d
```

15. 发布成功后，等那么两分钟，然后访问 **xxx.github.io**

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/14.png)

16. 创建文章的命令啥的就看上面的hexo官网就行，这里就不啰嗦了。

# 下期计划

- 把博客放到阿里云的OSS里
- CDN指向OSS，增加访问速度
- 写一个脚本，自动把master分支上传的阿里OSS上

# [OSS-CDN-GithubPages搭建自己的博客-下](https://lvjianqiao.top/2019/12/08/OSS-CDN-GithubPages%E6%90%AD%E5%BB%BA%E8%87%AA%E5%B7%B1%E7%9A%84%E5%8D%9A%E5%AE%A2-%E4%B8%8B/index.html)
