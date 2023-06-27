---
title: Window AVD + WSL2上运行React Native
date: 2020-11-15 21:38:39
tags: [WSL, React Native]
description: Windows Android Stdio 开启模拟器，WSL2上运行React Native start
---

# 背景
WSL2已经出了一些日子了，对比WSL1，WSL2是跑在虚拟机中的完整 Linux，所以功能方面要比WSL强大一些。并且linux环境刷Node问题会少些，比如node_sass不好安装、NodeModules路径名过长等等。

## WSL2 缺点

1. 进程不再会显示在任务管理器里，也无法在 WSL2 外操作，包括不再支持本机系统的防火墙规则。
2. 默认无法从其他机器访问到 WSL2 中监听的端口，需要在本机系统设置端口转发（如 netsh interface portproxy add ）。

# 开搞

## Windows wsl版本、adb版本、adb devices
![image](https://static.gezichenshan.top/blog/wsl/1.png)

## 确保Window 已经配好Android + RN的环境了
如果没配置好，可以参考[官方文档](https://reactnative.cn/docs/getting-started.html)，教程写的还是不错的。

## 配置WSL2中Android环境
**注意把代码中的<user>改为你自己的用户名噢，一共有三处**

**为了防止有些小伙伴没法下载sdk-tools-linux-4333796.zip, 我把这文件放到CDN上了，[点击这里下载](https://static.gezichenshan.top/blog/wsl/sdk-tools-linux-4333796.zip)**

```
cd /home/<user>/
sudo apt-get install unzip
wget https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip
unzip sdk-tools-linux-4333796.zip -d Android
rm sdk-tools-linux-4333796.zip
sudo apt-get install -y lib32z1 openjdk-8-jdk
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin
printf "\n\nexport JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64\nexport PATH=\$PATH:\$JAVA_HOME/bin" >> ~/.bashrc
cd Android/tools/bin
./sdkmanager "platform-tools" "platforms;android-26" "build-tools;26.0.3"
export ANDROID_HOME=/home/<user>/Android
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/platform-tools
printf "\n\nexport ANDROID_HOME=/home/<user>/Android\nexport PATH=\$PATH:\$ANDROID_HOME/tools\nexport PATH=\$PATH:\$ANDROID_HOME/platform-tools" >> ~/.bashrc
android update sdk --no-ui
sudo apt-get install gradle
gradle -v
adb start-server
```

## 在WSL2中映射Windows环境的ADB
> 如果不映射的话，会出现windows下powershell运行adb devices会出现模拟器，但是在wsl2中运行adb devices会出现空列表。如下图：

![image](https://static.gezichenshan.top/blog/wsl/2.png)

> 用vim打开.bashrc
```
sudo vim ~/.bashrc
```
> 设置adb.exe和fastboot.exe别名
```
alias adb='adb.exe'
alias fastboot='fastboot.exe'
```
> 重新载入
```
source .bashrc
```
> 在WSL2中运行abd devices，会出现跟powershell中一样的设备了，如下图

![image](https://static.gezichenshan.top/blog/wsl/3.png)

## 让WSL2的metro bundle可以被模拟器访问

> metro bundle运行在WLS2中，监听8081端口。有的时候模拟器无法直接连接到metro bundler，用下面的脚本在**powershell**中运行可以解决

```
iex "netsh interface portproxy delete v4tov4 listenport=8081 listenaddress=127.0.0.1" | out-null;
$WSL_CLIENT = bash.exe -c "ifconfig eth0 | grep 'inet '";
$WSL_CLIENT -match '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}';
$WSL_CLIENT = $matches[0];
iex "netsh interface portproxy add v4tov4 listenport=8081 listenaddress=127.0.0.1 connectport=8081 connectaddress=$WSL_CLIENT"
```
> 如果你想看WSL的IP地址，可以用下面的命令
```
echo $WSL_CLIENT
```

## 在WSL2中运行React Native
```
npx react-native start
```

此时在windows打开chrome，http://127.0.0.1:8081 和 http://<$WSL_CLIENT>:8081均可以监听到RN服务。

> $WSL_CLIENT就是上一步**echo $WSL_CLIENT**打印出来的本机WSL的ip地址

# 其他注意事项

## 把项目放在WSL2中
> 不要放在windows文件夹下，然后拿/mnt/c/xxx这种方式来访问项目，目前windows和WSL2通讯很慢的。直接在把项目放在WSL2中，你会感受到飞一般的速度。

> VSCODE可以安装**Remote - WSL**这个扩展，它可以打开你WSL2中的目录


# 参考资料
- [Building a react native app in WSL2【重点参考】](https://gist.github.com/bergmannjg/461958db03c6ae41a66d264ae6504ade#enable-access-to-metro-bundler-from-windows)
> 如果打不开上面的链接，[可以点这里](http://static.gezichenshan.top/blog/wsl/rearct-native-app-in-wsl2.md)

- [WSL 和 WSL2 简单对比](https://www.v2ex.com/t/587642)
- [WSL2 中使用adb fastboot 的方法](https://blog.csdn.net/haohaoyue01/article/details/106489387)
- [Winux之路-WSL 2的使用及填坑](https://zhuanlan.zhihu.com/p/224753478)