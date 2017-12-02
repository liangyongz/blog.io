---
layout: post
title: linux安装maven
date: 2017-11-30
categories: blog
tags: [linux,maven]
description: linux安装maven
---

# 前言

Apache Maven，是一个软件（特别是Java软件）项目管理及自动构建工具，由Apache软件基金会所提供。基于项目对象模型（POM）概念，Maven利用一个中央信息片断能管理一个项目的构建、报告和文档等步骤。曾是Jakarta项目的子项目，现为独立Apache项目。

# 安装

## maven下载

	[maven-3.0.5下载地址](http://ozupw8iis.bkt.clouddn.com/apache-maven-3.0.5-bin.tar.gz)
	
## 解压

	[root@instance-5tiad5rl baidudata]# tar zxvf apache-maven-3.0.5-bin.tar.gz
	
	[root@instance-5tiad5rl baidudata]# mv apache-maven-3.0.5 /usr/local/maven3
	
## 配置/etc/profile

	 vi /etc/profile
	 
新增配置文件如下

	MAVEN_HOME=/usr/local/maven3
	export MAVEN_HOME
	export PATH=${PATH}:${MAVEN_HOME}/bin
	 
执行source /etc/profile使环境变量生效

	[root@instance-5tiad5rl baidudata]# source /etc/profile
	 
## 验证

	[root@instance-5tiad5rl baidudata]# mvn -v


<img src="http://ozupw8iis.bkt.clouddn.com/201711301.png" align="center" class="img-responsive">

# The End
