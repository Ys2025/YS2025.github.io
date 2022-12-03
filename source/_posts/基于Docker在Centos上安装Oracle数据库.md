---
title: 基于Docker在Centos上安装Oracle数据库
date: 2021-12-25 14:56:07
tags: 
  - Redis

---

> Oracle数据库的占用太大，不想安装在物理机上，毕竟用的也不多，所以想把它装在虚拟机中的Centos服务器上，但是安装Linux版的太麻烦，所以为了简化安装过程，选择了在Docker中安装。

## 安装Docker

> 这一步没什么好说的，如果已经装过了Docker可以跳过此步骤。

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror aliyun	# 安装docker
systemctl start docker	# 启动Docker
```

## 拉取Oracle的镜像

> 该镜像很大，需要耐心等待....

```shell
docker pull registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g	# 拉取镜像，该步骤需要耐心等待...
docker images	# 查看镜像
```

![](https://img-blog.csdnimg.cn/20210710143937517.png#pic_center)

## 创建Oracle容器

```shell
docker run -d -p 1521:1521 --name oracle11g registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
```

## 进入容器切换为Root用户

>切换为Root用户。密码为：helowin

```shell
docker exec -it oracle11g bash		# 进入容器内部
su root								# 切换为Root用户。密码为：helowin
```

## 配置Oracle的环境变量

> 使用vi命令修改profile文件，配置Oracle的环境变量

```shell
vi /etc/profile						# 修改profile文件，配置Oracle的环境变量
```

配置内容如下

```shell
export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_2
export ORACLE_SID=helowin
export PATH=$ORACLE_HOME/bin:$PATH
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210710143909951.png?t_70#pic_center)

## 刷新配置文件，使其生效

```shell
source /etc/profile
```

## 

## 登陆sqlplus

```shell
su oracle
sqlplus /nolog
```

## 修改sys、system用户的密码

```sql
conn /as sysdba		-- 使用sysdba连接Oracle，最大的权限，os认证，只能在本机上登陆使用。
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210710144013638.png?t_70#pic_center)


```sql
alter user system identified by system;		-- 修改system用户的密码为systemalter user sys identified by sys;			-- 修改sys用户的密码为sysalter profile default limit password_life_time unlimited;	-- 默认口令是有180天的限制的，如果180没有修改密码，则该用户就无法登陆。此处是去除180密码限制。
```

## 创建orcl用户
> 到上面那一步就可以了，此步骤可有可无。

```sql
create user orcl identified by 123456;		-- 创建orcl用户，密码为123456
grant connect,resource,dba to orcl;			-- 给orcl用户赋予connect,resource,dba三个角色的权限。
```

## 使用PLSQL连接数据库

>数据库地址为：`服务器IP:1521/helowin`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210711164419757.png?t_70#pic_center)
