---
title: OSS-CDN-travisCI-自动化部署你的博客
date: 2020-05-18 22:49:04
tags:
top_img: https://gezichenshan.top/cms/bg
---

# 部署回顾

之前写了两篇OSS-CDN-GithubPage的部署教程，如果没看的请戳下面的链接，本次教程是接着原教程写的。

[OSS-CDN-GithubPages搭建自己的博客-上](https://lvjianqiao.top/2019/11/27/OSS-CDN-GithubPages%E6%90%AD%E5%BB%BA%E8%87%AA%E5%B7%B1%E7%9A%84%E5%8D%9A%E5%AE%A2-%E4%B8%8A/index.html)

[OSS-CDN-GithubPages搭建自己的博客-下](https://lvjianqiao.top/2019/12/08/OSS-CDN-GithubPages%E6%90%AD%E5%BB%BA%E8%87%AA%E5%B7%B1%E7%9A%84%E5%8D%9A%E5%AE%A2-%E4%B8%8B/index.html)

# 背景

当我们部署到oss上后，每次发版都需要运行部署的命令
```
hexo g -d
```

部署后还要下载master分支后的代码，然后把所有的资源传到OSS上，其实这些步骤 **Travis-CI** 都可以帮你做。省了不少事。

# 目标

hexo分支提交代码后，自动执行部署命令，并把部署后的代码上传到OSS上。换句话说，我们只需要写文章即可，发版构建上传通通不用操心。

# 步骤

1. 打开 [**官网**](http://travis-ci.org/) ，用Github注册登录Travis-ci。点击 Sign Up

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/30.png)

2. 用你的github授权Travis-ci

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/31.png)

3. 第一次进入需要点击 **ACTIVATE ALL REPOSITORIES USING GITHUB APPS**

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/32.png)

4. 然后可以选择是所有仓库都接入Travis CI还是指定仓库接入。我选择的是全部仓库。

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/33.png)

5. Approve后会让你重新登录一次，如下图

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/34.png)

6. 再认证一波

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/35.png)

7. 终于进来了，可以看到右边就是所有的仓库了

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/36.png)

8. 找到我们本次接入的仓库 **jianqiao.github.io** ，点击  **Settings**

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/37.png)

9. 在Environment Variables导入本次必备的变量

- accessKeyId：阿里云OSS需要的accessKeyId
- accessKeySecret：阿里云OSS需要的accessKeySecret
- bucket：阿里云OSS本次操作的bucket
- github_token：主要是用来hexo d的时候往master提交代码，否则没有权限，生成github_token可以见附录1
- HEXO_ALGOLIA_INDEXING_KEY：hexo algolia搜索用的admin的key，具体位置见附录2
- region：阿里云OSS的region

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/38.png)

10. 在 **hexo** 分支中增加 **.travis.yml** 文件
```
# 咱们使用的是nodejs
language: node_js
# 版本是10.19.0
node_js: "10.19.0"
# 在安装前打印当前目录的文件和路径，方便你理解构建环境
before_install:
  - ls -a
  - pwd
# 全局安装hexo-cli，因为我本地是3.1.0所以脚本定死版本了
install:
  - npm install -g hexo-cli@3.1.0
  - npm install
# 在执行脚本之前，我们需要设置algolia的环境变量，否则无法执行hexo algolia命令
before_script:
  - export HEXO_ALGOLIA_INDEXING_KEY=${HEXO_ALGOLIA_INDEXING_KEY}
# 在执行前，需要先下载Butterfly主体，当然，如果你用的不是我的这个主题，此部分可以删除。
# sed主要是为了替换gh_token用的，下面会讲到，这行的作用是把URL中gh_token替换成上面我们加的环境变量github_token
script:
  - git clone -b 2.3.5 https://github.com/jerryc127/hexo-theme-butterfly.git themes/Butterfly
  - hexo clean
  - hexo g
  - hexo algolia
  - git config user.name "jianqiao0313"
  - git config user.email "jianqiao0313@gmail.com"
  # 替换同目录下的_config.yml文件中gh_token字符串为travis后台刚才配置的变量，注意此处sed命令用了双引号。单引号无效！
  - sed -i "s/gh_token/${github_token}/g" ./_config.yml
  - hexo d

# 创建uploadToOss文件夹，然后clone你的博客的master分支，再clone上传阿里云的脚本。具体脚本内容请看附录3。然后运行index.js 把环境变量通过命令行参数的形式传递进去
after_script:
  - mkdir uploadToOss
  - cd uploadToOss
  - git clone https://github.com/jianqiao0313/jianqiao0313.github.io.git
  - git clone https://github.com/jianqiao0313/upload-oss-by-travis-ci.git
  - cd upload-oss-by-travis-ci
  - ls -a
  - pwd
  - npm i
  - node index.js publicPath=../jianqiao0313.github.io region=${region} accessKeyId=${accessKeyId} accessKeySecret=${accessKeySecret} bucket=${bucket}
  - echo "build finished"
# 只有指定的分支提交时才会运行脚本
branches:
  only:
    - hexo
# 邮件提醒，但截至20年5月19日 邮件服务好像有问题，见附录4
notifications:
  email:
    recipients:
      - jianqiao0313@gmail.com
      - jianqiao0313@126.com
    on_success: always
    on_failure: always


```

11. 修改 **_config.yml** 文件中deploy，gh_token就是我们上一步sed替换的标识，这样才能推送到master分支。
```
// 修改前
deploy:
  type: git
  repo: git@github.com:jianqiao0313/jianqiao0313.github.io.git
  branch: master

// 修改后
deploy:
  type: git
  repo: https://gh_token@github.com/jianqiao0313/jianqiao0313.github.io.git
  branch: master
```
12. 在你的hexo分支，随便写点东西，提交吧，Travis-CI会自动开始构建，体会持续集成的魅力。

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/41.png)


# 附录

## 附录1 Github Token如何生成

1. 点击右上角的 **Settings**

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/42.png)

2. 点击 **Developer settings**

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/43.png)

3. 点击 **Personal access tokens**, 点击**Generate new token**

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/44.png)

4. Note随便填，下面只需要勾选repo即可

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/46.png)

## 附录2 HEXO_ALGOLIA_INDEXING_KEY后台在图示处，点击复制按钮即可 

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/39.png)

## 附录3 上传阿里云OSS脚本

[Github地址](https://github.com/jianqiao0313/upload-oss-by-travis-ci)

```
// index.js
// 其实跟之前的文章区别不大，主要是把原来写死到文件里的参数，配置到了Travis-CI的环境变量中，通过Nodejs命令行进行读取。
const OSS = require('ali-oss');
const path = require('path');
const fs = require('fs');
const argvObj = process.argv.splice(2).reduce((obj, cur) => {
    item = cur.split("=");
    obj[item[0]] = item[1];
    return obj
}, {});
console.log("process.argv", process.argv);
console.log("argvObj", argvObj);
const publicPath = path.resolve(argvObj.publicPath);
const client = new OSS({
    region: argvObj.region,
    accessKeyId: argvObj.accessKeyId,
    accessKeySecret: argvObj.accessKeySecret,
    bucket: argvObj.bucket,
    timeout: 99999999
});
// 你可以修改Travis-CI脚本传入preDirPath或把jianqiao0313.github.io改为你仓库的名称，这个主要是用来过滤路径的。举例：把/home/travis/build/jianqiao0313/jianqiao0313.github.io/uploadToOss/jianqiao0313.github.io/index.html路径替换成index.html。因为OSS上传的时候需要传路径，如果这么一大长串都传上去，你就访问不了你的博客了。试一下就知道了
const preDirPath = argvObj.preDirPath || 'jianqiao0313.github.io'; 
```

## 附录4 邮件通知有问题

[官方论坛链接](https://travis-ci.community/t/e-mail-notification-does-not-work/8593/6)

希望官方尽快修好把。

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/47.png)

