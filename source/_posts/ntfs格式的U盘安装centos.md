---
title: ntfs格式的U盘安装centos
date: 2020-01-23 18:56:44
tags: linux
top_img: https://gezichenshan.top/cms/bg
---
# 目标

在一台物理机上安装Centos系统

# 前提

U盘不是exFat和ext4格式的，如果是这两个格式，大可不必单独分出一个区来存放centos镜像，我的U盘就是NTFS格式的。

# 前期准备

1. 1个安装了PE工具的U盘（PE工具如老毛桃、大白菜等）

![image](https://static.gezichenshan.top/blog/ntfs-centos/1.png)

2. centos镜像（放在ISO文件夹下,我下载的是minimal版）

![image](https://static.gezichenshan.top/blog/ntfs-centos/2.png)

![image](https://static.gezichenshan.top/blog/ntfs-centos/3.png)

# 步骤

## 进入WIN PE，用disk工具，分出一个可以容纳centos镜像的分区，分区格式要为EXT4

> 注：只有EXT4 可以在linux和windows上识别，但此种格式的分区，文单个文件不能大于4G，因此，如果镜像文件大于4G，可以改成exFat格式

## 把centos镜像拷入分好的分区中

> 注：因为是EXT4类型的分区，linux和PE都可以识别

## 然后在进入大白菜的界面，选【11】启动自定义ISO/IMG文件，如果找不到镜像文件，请按H，然后输入 **/ISO/**，如下图

![image](https://static.gezichenshan.top/blog/ntfs-centos/6.png)


## 获取硬盘编号。在安装centos的时候，在Install Centos选项上，按下TAB，然后修改屏幕下面的命令为vmlinuz initrd=initrd.img linux dd quiet（图示2）

![image](https://static.gezichenshan.top/blog/ntfs-centos/7.png)

## 查看刚才分区的编号，为sda1，如下图，记住后然后重启

![image](https://static.gezichenshan.top/blog/ntfs-centos/5.png)

> 注：ext4格式的就是刚才分区的盘，里面放的是centos镜像，ntfs是我的u盘，vfat是大白菜UEFI版本生成的PE系统

## 在Install Centos界面，按TAB，然后把下图的2修改为：vmlinuz initrd=initrd.img inst.stage2=hd:/sda1/ quiet
![image](https://static.gezichenshan.top/blog/ntfs-centos/7.png)

## 剩下的就是centos自己的安装流程了