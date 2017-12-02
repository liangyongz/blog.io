---
layout: post
title: 使用nexus搭建maven私服
date: 2017-11-30
categories: blog
tags: [linux,maven,nexus,eclipse]
description: 使用nexus搭建maven私服
---

# 前言

## Maven 仓库的分类：(maven的仓库只有两大类)

1.本地仓库 
2.远程仓库，在远程仓库中又分成了3种：
2.1 中央仓库 
2.2 私服 
2.3 其它公共库
有个maven私服可以很方便地管理我们的jar包和发布构建到远程仓库，本文就介绍了如何在linux下一步步使用nexus搭建maven私服。
私服是架设在局域网的一种特殊的远程仓库，目的是代理远程仓库及部署第三方构件。有了私服之后，当 Maven 需要下载构件时，直接请求私服，私服上存在则下载到本地仓库；否则，私服请求外部的远程仓库，将构件下载到私服，再提供给本地仓库下载。

安装nexus前请先安装jdk和maven

# 在linux安装nexus

## 下载nexus

所有版本下载地址

	[http://www.sonatype.org/nexus/archived/](http://www.sonatype.org/nexus/archived/)
	
我使用的是nexus-2.12.0-01,下面是安装包地址

		[nexus-2.12.0-01下载地址](http://ozupw8iis.bkt.clouddn.com/apache-maven-3.0.5-bin.tar.gz)

## 解压

	tar -zvxf nexus-2.12.0-01-bundle.tar.gz

注：解压后有两个文件夹：
               
nexus-2.12.0-01： 是nexus的核心文件

sonatype-work ：maven下载jar存放地址

## 启动

	 ./bin/nexus start
	 
默认情况下，不建议以root用户运行Nexus，可以修改bin/nexus中的配置跳过警告（修改RUN_AS_USER=root）,修改完重新启动即可

注：Nexus默认端口8081，如果想修改端口。修改/conf/nexus.properties文件


访问网址：http://IP:8081/nexus/#welcome,其中IP换成自己的服务器地址

# nexus登录 

点击右上角的 Log In 按钮即可登陆了。默认登录账号/密码为： admin/admin123 

点击Repositories，将列表中所有Type为proxy 的项目的 Configuration 中的 Download Remote Indexes 设置为True

<img src="http://ozupw8iis.bkt.clouddn.com/201712011.png" align="center" class="img-responsive">

将Releases仓库的Deployment Policy设置为 Allow ReDeploy

<img src="http://ozupw8iis.bkt.clouddn.com/201712012.png" align="center" class="img-responsive">

# 配置maven项目.xml文件

在setting.xml文件添加mirror:

```java
	  <localRepository>E:\maven\maven\repository</localRepository>
  
   <mirrors>
	<!-- nexu http://IP:8088/nexus/content/groups/public -->
	<mirror>
		<id>central</id>
		<name>central-mirror</name>
		<mirrorOf>*</mirrorOf>
		<url>http://IP:8081/nexus/content/repositories/central/</url>
	</mirror>
  </mirrors>
```

在settings.xml中配置server

```java
	 <servers>
    <server>
		<id>releases</id>
		<username>admin</username>
		<password>admin123</password>
    </server>	
    <server>
		<id>snapshots</id>
		<username>admin</username>
		<password>admin123</password>
    </server>	
  </servers>







## maven下载

	[maven-3.0.5下载地址](http://ozupw8iis.bkt.clouddn.com/apache-maven-3.0.5-bin.tar.gz)






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
