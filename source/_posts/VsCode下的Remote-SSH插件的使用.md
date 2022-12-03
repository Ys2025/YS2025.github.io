---
title: VsCode下的Remote-SSH插件的使用
date: 2021-12-25 15:49:24
tags:
  - SSH

---

## 0、前言

> 众所周知，Vs Code是一个非常NB的编辑器，它可以通过安装不同的插件，满足几乎所有的开发需求。最近了解到微软之前推出过一个Remote-SSH的插件，通过该插件可以在Vs Code上通过SSH连接Linux服务器进行终端操作或者文件编辑。所以本篇博客就是记录下怎么在Vs Code里面通过Remote-SSH插件连接Linux服务器并进行终端操作和文件编辑。

**系统环境：**

- 操作系统：Windows 10
- 服务器：Centos 7 64Bit
- 软件：Vs Code 1.61.2

## 1、安装Remote-SSH插件

打开Vs Code，插件商店搜索Remote-SSH安装即可

![](https://img-blog.csdnimg.cn/69a1292e05124259911f2c4f08fef94f.png?pic_center)


## 2、配置Linux服务器连接

![](https://img-blog.csdnimg.cn/8bcf5c336f19494eae9a67a7cbb68bd8.png?pic_center)


![](https://img-blog.csdnimg.cn/8f8399a77a7e46cea4a30047b4149b20.png?pic_center)


选择左侧小电脑，然后点击小齿轮，在弹出的选项中选择第一个即可。如图，第一个就表示将会把远程Linux服务器的连接地址以及用户信息保存在C:\Users\admin\\.ssh\config路径下。

![](https://img-blog.csdnimg.cn/5b565a3c214d47ef94badf8eeebd0527.png?pic_center)


- Host：主机名称
- HostName：主机地址
- User：用户

输入主机信息并保存后，在左侧列表就会出现配置的主机。

## 3、连接远程服务器

点击按钮连接服务器，打开新的窗口

![](https://img-blog.csdnimg.cn/746f685391984a8d87a5071a33594029.png?pic_center)


输入用户密码

![](https://img-blog.csdnimg.cn/48b611b275734c1ca65b814950b1298a.png?pic_center)


连接成功

![](https://img-blog.csdnimg.cn/153dc598f4e34d59b9561466bcc93b23.png?pic_center)


## 4、终端操作

![](https://img-blog.csdnimg.cn/07760fcbd24d459186bdeaf3e402de86.png?pic_center)


顶部菜单栏 终端——新建终端即可。

## 5、编辑文件

![](https://img-blog.csdnimg.cn/eed947be34bc4fb993472d1d2562c909.png?pic_center)


顶部菜单栏：文件——打开文件|打开文件夹——选择对应的路径点击确认即可。

<font color="red">注：点击确定后会让重新输入用户密码</font>

![](https://img-blog.csdnimg.cn/b4db78860d4b460bbc89ad1dc4bc6614.png?pic_center)


比如我打开的是home目录，打开后，可以在下方进行终端操作，左侧预览文件，上方直接编辑。



## 6、文件上传

![](https://img-blog.csdnimg.cn/7609028ea9b643d285d7a423293e36ec.png?pic_center)


文件上传同样很简单，直接把文件拖到左侧资源管理下的指定目录下即可。

## 到此结束
