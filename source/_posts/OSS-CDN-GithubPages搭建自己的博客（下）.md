---
title: OSS-CDN-GithubPages搭建自己的博客（下）
date: 2019-12-08 21:17:45
tags:
---
# [OSS-CDN-GithubPages搭建自己的博客（上）](https://lvjianqiao.top)

# [demo体验](https://lvjianqiao.top)

# 准备工作

- 开通阿里云CDN、OSS

- 有一个域名（非必须）

# 步骤

1. 开通OSS

起一个吊炸天的bucket的名字，选择标准存储、关闭、公共读。

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/15.png)

2. 设置bucket，支持静态页面, 静态首页改为index.html即可

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/23.png)

3. RAM控制访问增加一个用户（后面会用nodejs自动把博客上传oss用）

点击新建用户

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/16.png)

输入登录名称和显示名称，勾选编程访问，点击确定

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/17.png)

新建成功后，记得保存AccessKey ID和AccessKeySecret，也可以下载csv文件，这个页面关闭了就找不到这三个了，记得妥善保存。然后点击添加权限。

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/19.png)

权限选择AliyunOSSFullAccess

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/20.png)

4. 用Nodejs脚本来上传master分支的博客

```
// 使用之前 记得安装阿里云的依赖
npm i ali-oss --save
```


```
const OSS = require('ali-oss');
const path = require('path');
const fs = require('fs');
const publicPath = path.resolve('../master/jianqiao0313.github.io/'); // 填写博客master分支的路径
const client = new OSS({
    region: 'oss-cn-beijing',// bucket的区域选择哪里，这里就填那里，见下图
    accessKeyId: 'xxxxxxxxx', // 替换为刚才保存的AccessKey ID
    accessKeySecret: 'xxxxxxxxxx', // 替换为刚才保存的AccessKeySecret
    bucket: 'jianqiao0313github', // 刚才吊炸天的bucket的名字
    timeout: 99999999 // 超时时间改为99999999 设置一个比较大的值就行，防止超时
});
// 上传进度
let uploadProgress = 0;
let totalFile = 0;
async function list() {
    try {
        let res = [];
        // 不带任何参数，默认最多返回1000个文件。
        let result = await client.list();
        res = result.objects;
        // 根据nextMarker继续列出文件。
        while (result.isTruncated) {
            result = await client.list({
                marker: result.nextMarker
            });
            res = res.concat(result.objects);
        }
        // 如果res为undefined 代表oss已经为空了
        if (res) {
            delres = await deleteMulti(res.map(temp => temp.name))
        } else {
            readDir();
        }
    } catch (e) {
        console.log(e);
    }
}
async function deleteMulti(arr) {
    try {
        let result = await client.deleteMulti(arr, {
            quiet: true
        });
        console.log(`删除所有文件状态码：${result.res.statusCode}, 删除结果：${result.res.statusMessage}`);
        if (result.res.statusCode === 200) {
            readDir();
        }
    } catch (e) {
        console.log(`删除所有文件报错：${e}`);
    }
}
async function uploadFileToOss(_path, fileStream) {
    try {
        //object-name可以自定义为文件名（例如file.txt）或目录（例如abc/test/file.txt）的形式，实现将文件上传至当前Bucket或Bucket下的指定目录。
        let result = await client.put(_path, fileStream);
        console.log(`[${++uploadProgress}|${totalFile}]上传文件：${_path},状态码：${result.res.statusCode}，上传结果：${result.res.statusMessage}`);
    } catch (e) {
        console.log(`上传文件报错：${e}`);
    }
}
function readDir(proPath = '') {
    res = fs.readdirSync(path.resolve(publicPath, proPath));
    res.forEach(item => {
        if (item.indexOf('.git') === -1) {
            let _path = path.resolve(publicPath, proPath, item);
            fileStats = fs.statSync(_path);
            if (fileStats.isFile()) {
                totalFile++;
                let fileStream = fs.createReadStream(_path);
                _pathArr = _path.split(path.sep);
                dirIndex = _pathArr.indexOf('jianqiao0313.github.io');
                uploadFileToOss(_pathArr.splice(dirIndex + 1).join('/'), fileStream);
            } else {
                readDir(path.resolve(_path));
            }
        }
    })
}

list();


```

[阿里云 OSS region 对照表](https://www.cms2.cn/aliyun/3219.html)

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/21.png)


稍等一会儿就上传完了

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/22.png)

5. 开启CDN

添加域名

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/24.png)

创建成功后，会显示一个cname，把他添加到DNS里

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/25.png)


6. 修改DNS域名解析

添加两条CNAME，主机记录一个是@一个是www

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/26.png)

7. 等五分钟，ping一波，如果显示刚才cname的地址，就证明生效了，如下图

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/27.png)

8. 添加https证书支持

打开阿里云的SSL管理证书控制台，申请证书后，部署到CDN

![image](https://static.gezichenshan.top/blog/cdn-oss-githubPages/28.png)

9. 大功告成！访问一下试试吧

10. 访问后发现点击一些页面内的链接，跳转的都是首页，为啥呢，因为路径后面缺少index.html，把hexo-theme-butterfly的链接后面加上index.html即可，[详情见此次提交](https://github.com/jianqiao0313/hexo-theme-butterfly/commit/7b030972481036676964cb6af7053b002c6ebab6)