---
layout: post
title: linux安装配置zookeeper
date: 2017-11-29
categories: blog
tags: [linux,zookeeper]
description: linux安装配置zookeeper
---

Zookeeper是一个协调服务，可以用它来作为配置维护、名字服务、分布式部署。

# 安装

## Zookeeper下载
	
	[root@instance-5tiad5rl sbin]# wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.3.6/zookeeper-3.3.6.tar.gz 
	
## 解压
	
	[root@instance-5tiad5rl sbin]# tar -zxvf /baidudata/zookeeper-3.3.6.tar.gz 
	
解压之后baidudata目录会出现zookeeper-3.3.6目录

# 配置

## 拷贝zoo_samle.cfg为zoo.cfg

	[root@instance-5tiad5rl baidudata]# cd /baidudata/zookeeper-3.3.6/conf/
	[root@instance-5tiad5rl baidudata]# cp zoo_sample.cfg zoo.cfg  
	
## 修改zoo.cfg

文件修改如下

<img src="http://ozupw8iis.bkt.clouddn.com/201711291.png" align="center" class="img-responsive">

## 设置环境变量

	[root@instance-5tiad5rl baidudata]# export ZOOKEEPER_INSTALL=/baidudata/zookeeper-3.3.6
	[root@instance-5tiad5rl baidudata]# export PATH=$PATH:$ZOOKEEPER_INSTALL/bin 

## 启动

zookeeper启动和停止:

<img src="http://ozupw8iis.bkt.clouddn.com/201711292.png" align="center" class="img-responsive">

查询 zookeeper 状态并重启：

<img src="http://ozupw8iis.bkt.clouddn.com/201711293.png" align="center" class="img-responsive">

查看进程运行情况:

<img src="http://ozupw8iis.bkt.clouddn.com/201711294.png" align="center" class="img-responsive">

如果发现Zookeeper不是在运行状态的话，可以通过cat zookeeper.out来查看启动过程中的出错信息

# The End