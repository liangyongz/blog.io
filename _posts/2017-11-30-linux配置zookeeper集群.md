---
layout: post
title: linux配置zookeeper集群
date: 2017-11-30
categories: blog
tags: [linux,zookeeper]
description: linux配置zookeeper集群
---
# 前言
Zookeeper是一个协调服务，可以用它来作为配置维护、名字服务、分布式部署。

之前我已经在一台机器上配置了单节点的zookeeper,现在配置三台服务器,组成zookeeper集群

三台服务器,我们可以分别当做masterServer,slaverServerOne,slaverServerTwo,以下以其中一台为例

# 搭建

## 修改/etc/hosts 

修改操作系统的/etc/hosts文件中添加：
 
	127.0.0.1 masterServer
	127.0.0.1 slaverServerOne
	127.0.0.1 slaverServerTwo

注:127.0.0.1分别换成自己三个服务器的IP

## 编辑zoo.cfg

文件后增加如下信息:

	server.1=masterServer:2888:3888
	server.2=slaverServerOne:2888:3888
	server.3=slaverServerTwo:2888:3888

2888 端口号是 zookeeper服务之间通信的端口。3888 是 zookeeper与其他应用程序通信的端口。


initLimit：这个配置项是用来配置 Zookeeper接受客户端（这里所说的客户端不是用户连接Zookeeper 服务器的客户端，而是Zookeeper 服务器集群中连接到Leader的Follower服务器）初始化连接时最长能忍受多少个心跳时间间隔数。当已经超过10 个心跳的时间（也就是tickTime）长度后Zookeeper服务器还没有收到客户端的返回信息，那么表明这个客户端连接失败。总的时间长度就是5*2000=10秒。
 
syncLimit：这个配置项标识 Leader与 Follower之间发送消息，请求和应答时间长度，最长不能超过多少个tickTime 的时间长度，总的时间长度就是2*2000=4秒。
 
server.A=B:C:D：其中 A是一个数字，表示这个是第几号服务器；B是这个服务器的IP 地址或/etc/hosts 文件中映射了IP 的主机名；C 表示的是这个服务器与集群中的Leader 服务器交换信息的端口；D 表示的是万一集群中的Leader 服务器挂了，需要一个端口来重新进行选举，选出一个新的Leader，而这个端口就是用来执行选举时服务器相互通信的端口。如果是伪集群的配置方式，由于B 都是一样，所以不同的Zookeeper实例通信端口号不能一样，所以要给它们分配不同的端口号。

## 创建 myid文件

根据zoo.cfg配置路径:
dataDir=/usr/zookeeper/zkdata
dataLogDir=/usr/zookeeper/log

到zkdata/下新建myid文本,编辑myid 文本并且写上service后的数字

## 开放防火墙端口2181、2888、3888

	[root@instance-5tiad5rl sbin]# vi /etc/sysconfig/iptables

增加三行:

	-A INPUT -m state --state NEW -m tcp -p tcp --dport 2181 -j ACCEPT 
	-A INPUT -m state --state NEW -m tcp -p tcp --dport 2888 -j ACCEPT 
	-A INPUT -m state --state NEW -m tcp -p tcp --dport 3888 -j ACCEPT
 
重启防火墙：

	[root@instance-5tiad5rl sbin]# service iptables restart

查看防火墙端口状态：
	
	[root@instance-5tiad5rl sbin]# service iptables status
	
最后启动zookeeper测试下

# The End