---
title: 使用XShell连接Vm虚拟机中安装的Centos
date: 2021-12-25 15:59:38
tags:
  - Centos
  - Xhsell
---

## 1、配置网络模式

配置虚拟机中的Centos系统的网络适配器

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210703213343909.png?t_70#pic_center)




## 2、配置VM的网络虚拟机

VM软件→编辑→虚拟网络编辑器

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210703213428205.png?t_70#pic_center)


子网IP不一定要使用10.0.0.0，此处使用10.0.0.0是为了避免和其他IP冲突。

网关IP需要在子网IP网段中。

## 3、配置V8虚拟网络适配器

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210703213450336.png?t_70#pic_center)


IP地址需要在第二步骤配置的子网IP网段中。

## 4、配置Centos静态IP

启动服务器，使用`ip addr`查看网卡名称

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210703213506411.png?t_70#pic_center)


比如我的网卡名称就叫做`ens33`，并且大部分机器都是`ens33`

进入到`/etc/sysconfig/network-scripts`，使用vi命令编辑`ifcf-ens33`文件。

如下

```properties
YPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=3e502ba4-72ed-4b45-8009-41c69c6d7e25
DEVICE=ens33
ONBOOT=yes
IPADDR=10.0.0.100
NETMASK=255.255.255.0
GATEWAY=10.0.0.1
DNS1=114.114.114.114
DNS2=8.8.8.8
```

主要修改一下几个配置

- BOOTPROTO=static：使用静态ip设置
- IPADDR=10.0.0.100：Centos服务器的IP，此IP需要在第二部配置的子网IP网段内。并且后期使用XShell连接时，就是连接此IP
- NETMASK=255.255.255.0：同第二步配置的子网掩码
- GATEWAY=10.0.0.1：同第二步配置的网关地址
- DNS1=114.114.114.114：DNS地址，配置为114.114.114.114即可
- DNS2=8.8.8.8：配置为8.8.8.8即可
- ONBOOT=yes：配置为YES即可
