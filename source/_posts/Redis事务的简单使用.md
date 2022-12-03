---
title: Redis事务的简单使用
date: 2021-12-25 14:56:07
tags: 
  - Redis
---
**问题：如果`Java`代码出现了异常，怎么对`Redis`进行回滚？**

一次和朋友聊天聊到了这个问题，当时第一想法就是，`try-catch`异常，在`catch`里对之前插入到`Redis`的数据进行删除操作。但是接下来又有一个问题：**如果在删除时报错了怎么办？**

## 什么是事务？

学过关系型数据库的应该都知道，事务有一个`ACID`原则，即事务的四大特性：

1. `atomicity(原子性)`：一个事务是一个不可分割的工作单位，事务中包括的操作要么都做，要么都不做
2. `consistency(一致性)`：事务必须是使数据库从一个一致性状态变到另一个一致性状态。一致性与原子性是密切相关的。
3. `isolation(隔离性)`：一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。
4. `durability(持久性)`：指一个事务一旦提交，它对数据库中数据的改变就应该是永久性的。接下来的其他操作或故障不应该对其有任何影响。

上面的`ACID`指的是关系型数据库，但是`Redis`作为一个`NoSQL(非关系型数据库)`，它的事务肯定是和关系型数据库的不一样。

`Redis`的事务很简单，简单到它的本质其实就是一个队列。流程如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/24aa084000644b57b4acb55da74c110a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZWl5Lmf5LiN5Lya55qE56iL5bqP5ZGYQ29kZQ==,size_12,color_FFFFFF,t_70,g_se,x_16#pic_center)


需要说明一下，加入到队列中的`Redis`命令并不会执行，只有输入了`EXEC(提交事务)`的命令后，才会依次执行队列中的命令。

**事务的三个阶段：**

1. 开始事务
2. 命令入对
3. 执行事务

## Redis事务的简单使用

1. 使用`multi`命令，开启事务
2. 输入`redis`命令
3. 使用`exec`命令，提交事务

```shell
# 查看所有的Key
127.0.0.1:6379> keys *
(empty list or set)
# 开启事务
127.0.0.1:6379> multi
OK
# set name = zhangsan
127.0.0.1:6379> set name zhangsan
QUEUED
# set age = 18
127.0.0.1:6379> set age 18
QUEUED
# 提交事务
127.0.0.1:6379> exec
1) OK
2) OK
# 查看所有的Key
127.0.0.1:6379> keys *
1) "name"
2) "age"
```

可以看到，开启事务之前，什么数据都没有，只有执行`EXEC(提交事务)`命令后，才会依次执行被事务包含的命令，并打印`OK`。

## Redis事务的“原子性”

`atomicity(原子性)`的要求就是，要么全部执行，要么全部不执行。但是`Redis`的原子性却有所不同，当事务提交提交之后，会依次执行队列中的命令，如果队列中的其中一个命令执行出错，并不会影响到其他的命令。显然这个不满足原子性的条件，所以`Redis事务不是原子性`的。

```shell
# 查看所有的Key
127.0.0.1:6379> keys *
(empty list or set)
# 开启事务
127.0.0.1:6379> multi
OK
# set name = zhangsan
127.0.0.1:6379> set name zhangsan
QUEUED
# set age = 18
127.0.0.1:6379> set age 18
QUEUED
# 将key为name的值+1
127.0.0.1:6379> incr name
QUEUED
# 将key为age的值+1
127.0.0.1:6379> incr age
QUEUED
# 提交事务
127.0.0.1:6379> exec
1) OK
2) OK
3) (error) ERR value is not an integer or out of range
4) (integer) 19
# 查看所有的Key
127.0.0.1:6379> keys *
1) "name"
2) "age"
# get name
127.0.0.1:6379> get name
"zhangsan"
# get age
127.0.0.1:6379> get age
"19"
127.0.0.1:6379>
```

可以看到，执行时报了`(error) ERR value is not an integer or out of range`错误，但是整体来看，只有`incr age`命令执行失败其它的都是执行成功 。所以可以看到，确实不符合`原子性`的要求。

注：`Redis`的单条命令是原子性的，但是事务不是原子性的。

## Redis的事务“回滚”

严格来说，`Redis`的事务是不能回滚的。`Redis`只提供了一个`DISCARD`命令，作用就是取消事务。前面提到过，`Redis`的事务本质就是一个队列，开启事务之后的所有命令都是被放在队列中，只有执行`EXEC(提交事务)`命令，才会依次执行队列中的命令。执行`DISCARD`命令时，会放弃队列中的所用命令，结束本次事务，所以严格来说，`Redis`的事务是不能回滚的，只能取消，且取消操作需要在执行`EXEC(提交事务)`命令之前。

```shell
# 查看所有的Key
127.0.0.1:6379> keys *
(empty list or set)
# 开启事务
127.0.0.1:6379> multi
OK
# set name = zhangsan
127.0.0.1:6379> set name zhangsan
QUEUED
# 取消事务
127.0.0.1:6379> discard
OK
# 查看所有的Key
127.0.0.1:6379> keys *
(empty list or set)
```

可以看到，开启事务之前什么数据都没有，执行`DISCARD(取消事务)`命令后，也是什么数都没插入。

## 	Redis事务的WATCH和Unwatch命令

>`WATCH`：监视一个或者多个`Key`，如果在事务执行之前，被监控的`Key`的值如果被改变，则事务将会被打断。
>
>`Unwatch`：放弃监控。

### WATCH

监视Name

```shell
# 查看所有的Key
127.0.0.1:6379> keys *
(empty list or set)
# 监视 key = name
127.0.0.1:6379> watch name
OK
# 开启事务
127.0.0.1:6379> multi
OK
# set name = zhangsan
127.0.0.1:6379> set name zhangsan
QUEUED
# set age = 18
127.0.0.1:6379> set age 18
QUEUED
```

创建一个新的绘画，修改name的值

```shell
# set name = list
127.0.0.1:6379> set name lisi
OK
```

提交事务

```shell
# 提交事务
127.0.0.1:6379> exec
(nil)
# 查看所有的Key
127.0.0.1:6379> keys *
1) "name"
# get name
127.0.0.1:6379> get name
"lisi"
```

可以看到，和之前的不同，执行`exec`后返回的不是`OK`而是`nil`。同时 事务中设置的name和age的值也不存在，只有在另一个会话中set的name。可以得出结论：`被监控的Key的值如果被改变，则整个事务将会执行失败`。

### Unwatch

终端A

```shell
# 查看所有的Key
127.0.0.1:6379> keys *
(empty list or set)
# 监控 key = name
127.0.0.1:6379> watch name
OK
# 取消监视
127.0.0.1:6379> unwatch
OK
# 开启事务
127.0.0.1:6379> multi
OK
# set name = zhangsan
127.0.0.1:6379> set name zhangsan
```

终端B

```shell
# set name = list
127.0.0.1:6379> set name list
OK
```

终端A

```shell
# set name wanger
127.0.0.1:6379> set name wanger
QUEUED
# 提交事务
127.0.0.1:6379> exec
1) OK
2) OK
# 查看所有的Key
127.0.0.1:6379> keys *
1) "name"
# get name
127.0.0.1:6379> get name
"wanger"
```

可以看到，执行了`unwatch`命令后，在终端B修改`name`的值，不会出现整个事务失败的问题。

## 总结

- Redis的单条命令支持原子性，但是事务不支持原子性。
- Redis的事务是不能回滚的，但是可以取消事务(放弃事务)。
- Redis事务有四个方法
  - DISCARD：取消事务(放弃事务)
  - EXEC：提交事务(执行事务)
  - MULTI：开启事务
  - UNWATCH：取消所有监视
  - WATCH：监视1个或者多个Key

