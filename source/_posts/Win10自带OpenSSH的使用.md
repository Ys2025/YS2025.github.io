---
title: Win10自带OpenSSH的使用
date: 2021-12-25 15:46:26
tags: 
  - SSH
---

## 0、前言

> 昨天写了一篇文章，记录了使用Vs Code的Remote-SSH的插件连接远程Linux服务器的方法。后来觉得还是很麻烦，需要先装Vs Code其次又要装Remote-SSH插件。今天网上查了查，其中有一篇文章提到，微软官方在2018年4月份更新的Win10版本中加入了正式版的OpenSSH，并默认安装在Win10系统中。所以本篇文章就是为了记录怎么在Win10默认环境下，使用OpenSSH远程连接Linux服务器。

**系统环境：**

- 操作系统：Windows 10 21H1
- 服务器：Centos 7 64Bit

## 1、检查是否安装有OpenSSH

1. 打开系统的设置页面，可以使用快捷键Win+I打开。
2. 应用—可选功能
   - ![](https://img-blog.csdnimg.cn/f23ca096223c44539068687d924633ad.png?pic_center)

3. 查看是否安装
   - ![](https://img-blog.csdnimg.cn/c93ddc96d8c54ca9b1b40b29255c27cd.png?pic_center)

   - 如果已安装中列表中没有OpenSSH，则点击添加功能按钮进行添加。



## 2、连接服务器

> 三种方式，但其本质是一致的。
>
> 连接命令：`ssh user@host`，默认端口为22，如要指定端口号，则`ssh user@host -p port`

###  1、运行窗口连接

Win+R打开运行窗口，输入连接命令，点击确定即可。

![](https://img-blog.csdnimg.cn/a5a06765cff546998b2baf12a7bc9209.png?pic_center)


### 2、CMD命令窗口连接

![](https://img-blog.csdnimg.cn/518c260d57f24f6c848a177cc9d4d969.png?pic_center)


### 3、Bat批处理

```bash
:: 关闭回显
@echo off
echo 啥也不会的程序员
:: User,Linux用户
set User=root
:: Host,Linux服务器地址
set Host=10.0.0.100
:: Port，Linux服务器端口
set Port=22
:: ssh连接
ssh %User%@%Host% -p %Port%
pause
```

## 3、登出

> Ctrl+D或者logout命令即可

## 4、文件上传

> 命令：`scp filePath user@host`

![](https://img-blog.csdnimg.cn/07c1b521310f441dac2e82cd54ad6496.png?pic_center)


## 5、文件下载

> 命令：`scp user@host:filePath outFilePath`

![](https://img-blog.csdnimg.cn/55c04baaf37a4763b10ce79e6d501c71.png?pic_center)




## 到此结束
