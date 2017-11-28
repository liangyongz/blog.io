---
layout: post
title: CentOS6.5安装Redis
date: 2017-11-28
categories: blog
tags: [linux，redis]
description: CentOS6.5安装Redis
---
# 前言

Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

redis是一个key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set –有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

# Redis安装

## 1.1 redis下载

	[root@instance-5tiad5rl ~]# wget http://download.redis.io/releases/redis-3.0.5.tar.gz
	
## 1.2 redis解压

	[root@instance-5tiad5rl ~]# tar xf redis-3.0.5.tar.gz

这样就在当前目录下新建了一个包含发行版源代码的目录，必须cd进入这个目录以继续服务器的编译。

## 1.3 编译及安装

	[root@instance-5tiad5rl ~]# make
	[root@instance-5tiad5rl ~]# make install
	
这时会把这些可执行程序拷贝到/usr/local/bin目录下，由于/usr/local/bin是在系统的环境变量$PATH下定义的，因此终端在任意位置就可以执行redis-server和redis-cli了。

至此redis安装就已初步完成,我们可以看下/usr/local/bin/下几个程序分别是做什么的,这里只做一个简单记录.

redis-server：即我们的redis服务,最重要的一个服务

redis-cli：redis client，提供一个redis客户端，以供连接到redis服务，进行增删改查等操作

redis-sentinel：redis实例的监控管理、通知和实例失效备援服务

redis-benchmark：redis的性能测试工具

redis-check-aof：若以AOF方式的持久化，当意外发生时用来快速修复

redis-check-rdb：若以RDB方式的持久化，当意外发生时用来快速修复

## 1.4 配置文件移动

文件放在固定目录便于管理,也可以不这么做,不过建议自己管理一下命令和配置,文件路径如下:

配置文件: vi /etc/redis/redis.conf
dump file、进程pid、log目录等，一般放在/var/redis/

<img src="http://ozupw8iis.bkt.clouddn.com/201711283.png" align="center" class="img-responsive">

## 1.5 redis启动及关闭
	
安装完成后,启动redis-server,并运行redis-cli进行连接测试:

	[root@instance-5tiad5rl ~]# redis-server /etc/redis/redis.conf
	[root@instance-5tiad5rl ~]# redis-cli

<img src="http://ozupw8iis.bkt.clouddn.com/201711281.png" align="center" class="img-responsive">

随便往里面插入一个值测试一下，可以正常获取，说明客户端没有问题。退出客户端的话直接quit即可。

<img src="http://ozupw8iis.bkt.clouddn.com/201711282.png" align="center" class="img-responsive">

关闭redis可以使用如下命令

	[root@instance-5tiad5rl ~]# pkill redis-server
	
或
	
	[root@instance-5tiad5rl ~]# redis-cli shutdown

可使用命令查看进程是否存在

	[root@instance-5tiad5rl ~]# ps -ef | grep redis


# redis配置

## 2.1 redis.conf配置

	vi /etc/redis/redis.conf 

查找daemonize no改为  

	daemonize yes  
	
以守护进程方式运行 

修改dir ./为绝对路径,默认的话redis-server启动时会在当前目录生成或读取dump.rdb,所以如果在根目录下执行redis-server /etc/redis.conf的话,读取的是根目录下的dump.rdb,为了使redis-server可在任意目录下执行:

	dir /var/redis

指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。
修改appendonly为yes 

为了让redis-server能在系统启动时自动运行，需要将redis服务作为守护进程（daemon）来运行，我们回到/etc/redis/目录中找到一个redis.conf的文件，这个文件是redis服务运行时加载的配置，我们先观察一下其中的内容

此文件内容非常长，但是大部分是注释，我们重点关注其中的几个设置daemonize和pidfile：

其中daemonize默认值是false，pidfile默认值是pidfile /var/run/redis_6379.pid

第一个表示是否daemon化，显然我们要把它改成daemonize yes；

第二个表示当服务以守护进程方式运行时，redis默认会把pid写入/var/run/redis_6379.pid文件，服务运行中该文件就存在，服务一旦停止该文件就自动删除，因而可以用来判断redis是否正在运行。

保存后退出。

## 2.2 redis_init_script设置
redis源码里其实已经提供了一个初始化脚本，用来管理启动、关闭、重启,位置在安装目录下,我的在/data/redis-3.0.5/utils/redis_init_script

<img src="http://ozupw8iis.bkt.clouddn.com/201711284.png" align="center" class="img-responsive">

脚本中指定了端口、server路径、cli路径、pidfile路径以及conf路径，上述标注的地方都需要正确配置，多说一句，如果在安装时执行了make install，那么这里的脚本不需要做多大改动，因为make install把server和cli都拷到/usr/local/bin下面了。

另外看到这里conf的路径，我们需要把redis目录下的redis.conf文件拷贝到/etc/redis/6379.conf

<img src="http://ozupw8iis.bkt.clouddn.com/201711285.png" align="center" class="img-responsive">

我这里之前已经放了一个redis.conf,不影响

接着将redis_init_script脚本拷贝到/etc/init.d/redisd

	[root@instance-5tiad5rl redis]# cp /baidudata/redis-3.0.5/utils/redis_init_script /etc/init.d/redisd

在/etc/init.d下的脚本都是可以在系统启动是自动启动的服务，而现在还缺一个系统启动时的配置：

	[root@instance-5tiad5rl redis]# chkconfig redisd on

发现报错如下:

<img src="http://ozupw8iis.bkt.clouddn.com/201711286.png" align="center" class="img-responsive">

解决办法是在redis_init_script的开头加如下配置:

	#!/bin/sh
	# chkconfig: 2345 90 10 
	# description: Redis is a persistent key-value database

上面的注释的意思是，redis服务必须在运行级2，3，4，5下被启动或关闭，启动的优先级是90，关闭的优先级是10。

保存完重新拷贝到/etc/init.d/redisd后，再运行chkconfig就完成了。

<img src="http://ozupw8iis.bkt.clouddn.com/201711287.png" align="center" class="img-responsive">

一切就绪之后，可以执行以下命令检验service是否设置成功:

	[root@instance-5tiad5rl redis]# service redisd start
	[root@instance-5tiad5rl redis]# service redisd stop

如下:

<img src="http://ozupw8iis.bkt.clouddn.com/201711288.png" align="center" class="img-responsive">

最后重启一下系统吧，进入系统之后直接运行redis-cli检验redis服务是否已经自动运行了。

## 2.3 开放redis端口

关闭防火墙

	[root@instance-5tiad5rl redis]# service iptables stop 

进入iptables

	[root@instance-5tiad5rl redis]# vi /etc/sysconfig/iptables

添加信任端口

	-A INPUT -m state --state NEW -m tcp -p tcp --dport 6379 -j ACCEPT

重启防火墙

	[root@instance-5tiad5rl redis]# service iptables restart 

## 2.4 redis设置密码
出于安全考虑,我设置了redis的密码,具体步骤如下

不论是/etc/redis/redis.conf ,还是/etc/redis/6379.conf都一样,这里以redis.conf为例

	vi /etc/redis/redis.conf 
	
找到#requirepass foobared  

去掉行前的注释，并修改密码为所需的密码,保存文件

	requirepass 123456

使用我们之前的命令重启redis,如下所示,最开始的不输入密码登录redis-cli,提示没有权限,-a后面就是刚刚设置的密码

<img src="http://ozupw8iis.bkt.clouddn.com/201711289.png" align="center" class="img-responsive">

另外一种设置密码的方法就是登入redis-cli,使用如下set密码即可,这种方法无需重启redis

<img src="http://ozupw8iis.bkt.clouddn.com/2017112810.png" align="center" class="img-responsive">

## The End