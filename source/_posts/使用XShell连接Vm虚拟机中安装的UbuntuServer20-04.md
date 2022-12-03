---
title: 使用XShell连接Vm虚拟机中安装的UbuntuServer20.04
date: 2021-12-25 15:59:04
tags:
  - Ubuntu
  - Xhsell
---

## 1、配置网络桥接模式

配置虚拟机中的Ubuntu系统的网络适配器

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210704163335573.png?t_70#pic_center)




## 2、配置VM的网络虚拟机

VM软件→编辑→虚拟网络编辑器

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210704163414510.png?t_70#pic_center)


子网IP不一定要使用10.0.0.0，此处使用10.0.0.0是为了避免和其他IP冲突。

网关IP需要在子网IP网段中。

## 3、配置V8虚拟网络适配器

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210704163430155.png?t_70#pic_center)


IP地址需要在第二步骤配置的子网IP网段中。

## 4、配置Ubuntu静态IP

进入到`/etc/netplan`路径，使用`vim`命令编辑`yaml`网络配置文件。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210704163447782.png#pic_center)


红框为网络配置文件，建议在修改网络配置文件之前，对源文件进行备份。

内容如下

```yaml
network:
  ethernets:
    ens33:
      dhcp4: false
      addresses: [10.0.0.110/24]
      gateway4: 10.0.0.1
      nameservers:
              addresses: [114.114.114.114,8.8.8.8]
  version: 2
```

主要修改一下几个配置

- dhcp4: false：不使用dhcp服务(动态IP分配)
- addresses: [10.0.0.110/24]：Ubuntu服务器的IP，此IP需要在第二部配置的子网IP网段内。并且后期使用XShell连接时，就是连接此IP
- gateway4: 10.0.0.1：同第二步配置的网关地址
- addresses: [114.114.114.114,8.8.8.8]：DNS地址，配置为114.114.114.114,8.8.8.8即可
