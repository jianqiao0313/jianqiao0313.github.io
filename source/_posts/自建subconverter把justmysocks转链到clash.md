---
title: 自建subconverter把justmysocks转链到ClashForAndroid
date: 2022-06-22 13:13:13
tags: [subconverter, justmysocks, clash, 订阅, subscribe]
description: ClashForAndroid不支持justmysocks的订阅链接，需要进行转链操作
---
# 部署后的地址(欢迎使用)

[https://subconverter.gezichenshan.top](https://subconverter.gezichenshan.top)


[https://sub.gezichenshan.top](https://sub.gezichenshan.top)


# 背景

安卓机子一直用的v2RayNG，界面有些简陋，打算换成[ClashForAndroid](https://github.com/yichengchen/clashX) 。
使用的过程中，把justmysocks的订阅链接导入后，发现ClashForAndroid不识别，在ClashForAndroid的[issues](https://github.com/Kr328/ClashForAndroid/issues/1383)上，发现作者不打算支持v2ray的订阅。

只能用转链工具进行转链了。

# 搭建转链服务

github上比较火的转链工具是[subconverter](https://github.com/tindy2013/subconverter)

## 下载 & 部署

按下面的命令就可以在linux系统上部署了

```
# 下载release
wget https://github.com/tindy2013/subconverter/releases/latest/download/subconverter_linux64.tar.gz

# 解压并授权
tar -xvzf subconverter_linux64.tar.gz subconverter && chmod +x subconverter/subconverter

# 运行即可
subconverter/subconverter

```

## 改成systemctl服务，开启自动启动

```
# 创建subconverter.service
vim /etc/systemd/system/subconverter.service
```

```
# /etc/systemd/system/subconverter.service 文件内容如下
# 修改ExceStart和WorkingDirectory的地址，其他不用动
[Unit]
Description=A API For Subscription Convert
After=network.target

[Service]
Type=simple
ExecStart=/root/subconverter/subconverter
WorkingDirectory=/root/subconverter
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

```
# 使用systemctl查看subconverter服务的状态
systemctl daemon-reload
systemctl start subconverter
systemctl status subconverter
systemctl enable subconverter // 开启自启
```

## nginx反向代理（增加https证书）

```
server {
    listen 80;
    server_name subconverter.gezichenshan.top;
    #add_header Strict-Transport-Security max-age=15768000;
    #永久重定向到 https 站点
    return 301 https://$server_name$request_uri;
}
server {
    listen 443;
    server_name subconverter.gezichenshan.top;
    ssl on;
    root html;
    index index.html index.htm;
    ssl_certificate cert/7986493_subconverter.gezichenshan.top.pem;
    ssl_certificate_key cert/7986493_subconverter.gezichenshan.top.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location / {
        proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   REMOTE-HOST $remote_addr;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://127.0.0.1:25500;
    }
}

```

## subconverter部署成功

[https://subconverter.gezichenshan.top](https://subconverter.gezichenshan.top)

# 搭建sub-web

[sub-web](https://github.com/CareyWang/sub-web)是一个图形化的操作界面，主要用来生成转链地址，可以配合subconverter使用

## 本地运行 
```
git clone https://github.com/CareyWang/sub-web.git

yarn install

yarn serve
```

## 修改后端地址
```
// .env文件 修改VUE_APP_SUBCONVERTER_DEFAULT_BACKEND这个属性

# API 后端
VUE_APP_SUBCONVERTER_DEFAULT_BACKEND = "https://subconverter.gezichenshan.top"
```

## build
```
yarn build
```

## 修改nginx
```
# 需要修改root的属性，改成部署后的文件夹地址
server {
    listen 80;
    server_name sub.gezichenshan.top;
    #add_header Strict-Transport-Security max-age=15768000;
    #永久重定向到 https 站点
    return 301 https://$server_name$request_uri;
}
server {
    listen 443;
    server_name sub.gezichenshan.top;
    ssl on;
    root /root/sub-web/dist;
    index index.html index.htm;
    ssl_certificate cert/7987229_sub.gezichenshan.top.pem;
    ssl_certificate_key cert/7987229_sub.gezichenshan.top.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
}

```

## sub-web部署成功

[https://sub.gezichenshan.top](https://sub.gezichenshan.top)