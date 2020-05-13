---
title: miniapp-jenkins-ci
date: 2020-04-06 22:29:09
tags: 小程序
top_img: https://gezichenshan.top/cms/bg
---
# centos系统部署jenkins

# 准备
- docker（docker 要求 CentOS 系统的内核版本高于 3.10 ）
- 本次操作系统是centOS 7.4

# 安装步骤

## 拉取官方镜像

```
    docker pull jenkins/jenkins
```

![image](https://static.gezichenshan.top/blog/linux/centos-jenkins-0.png)


## 创建jenkins数据目录，并把文件夹权限变为777

```
    mkdir /root/jenkins_node
    chmod 777 /root/jenkins_node
```

![image](https://static.gezichenshan.top/blog/linux/centos-jenkins-1.png)

## 运行jenkins

```
    docker run -d --name jenkins -p 8081:8080 -p 50000:50000 -v /root/jenkins_node:/var/jen
```
![image](https://static.gezichenshan.top/blog/linux/centos-jenkins-2.png)

## 访问localhost:8081

![image](https://static.gezichenshan.top/blog/linux/centos-jenkins-3.png)

## 查看jenkins密码，并拷贝到页面上，点击继续

```
    cat /root/jenkins_node/secrets/initialAdminPassword
```

![image](https://static.gezichenshan.top/blog/linux/centos-jenkins-4.png)

![image](https://static.gezichenshan.top/blog/linux/centos-jenkins-5.png)

## 选择社区推荐的插件

![image](https://static.gezichenshan.top/blog/linux/centos-jenkins-6.png)

## 稍等片刻，插件安装中

![image](https://static.gezichenshan.top/blog/linux/centos-jenkins-7.png)
