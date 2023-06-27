---
title: WSL2中设置使用windows的代理
date: 2020-11-22 11:49:44
tags: [WSL]
description: 有时候WSL2中要下载一些被Qiang的资源，需要连到windows的代理上
---

# 准备工作
## V2Ray设置 允许来自互联网的连接
![image](https://static.gezichenshan.top/blog/wsl/proxy-1.png)
## 记下V2Ray本地的IP
![image](https://static.gezichenshan.top/blog/wsl/proxy-2.png)

# 新增两个脚本
> start_proxy.sh（开启代理的脚本）
``` 
#!/bin/sh
# 获取wsl2 ip地址
hostip=$(cat /etc/resolv.conf | grep nameserver | awk '{ print $2 }')
# 设置v2ray的IP（准备工作第二步）
port=10809
PROXY_HTTP="http://${hostip}:${port}"

export http_proxy="${PROXY_HTTP}"
export HTTP_PROXY="${PROXY_HTTP}"

export https_proxy="${PROXY_HTTP}"
export HTTPS_proxy="${PROXY_HTTP}"
```

> stop_proxy.sh（关闭代理的脚本）
```
#!/bin/sh
unset http_proxy
unset HTTP_PROXY
unset https_proxy
unset HTTPS_proxy
```

# 给这两个脚本增加权限
```
chmod 777 start_proxy.sh
chmod 777 stop_proxy.sh
```

# 打开代理
```
source start_proxy.sh
```
> 运行**export**查看是否添加成功，如下图

![image](https://static.gezichenshan.top/blog/wsl/proxy-3.png)

> 运行**curl -vv google.com**查看代理是否正常

![image](https://static.gezichenshan.top/blog/wsl/proxy-4.png)

# 关闭代理
```
source stop_proxy.sh
```
> 运行**export**查看是否删除成功，如下图

![image](https://static.gezichenshan.top/blog/wsl/proxy-5.png)

> 运行**curl -vv google.com**查看是否正常关闭代理，无法访问

![image](https://static.gezichenshan.top/blog/wsl/proxy-6.png)


