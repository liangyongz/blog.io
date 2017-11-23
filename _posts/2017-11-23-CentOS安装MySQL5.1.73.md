---
layout: post
title: CentOS6.5安装MySQL5.1.73
date: 2017-11-23
categories: blog
tags: [linux，mysql]
description: CentOS6.5安装MySQL5.1.73
---
## 一、检查系统原有版本并卸除
<br />

	rpm -qa | grep mysql
	
卸载有两种方式，一种是普通删除:
	
	rpm -e mysql
	
另一种是强力删除，当MySQL数据库有其它的依赖文件时，也进行删除:

	rpm -e --nodeps mysql
	
<br />

## 二.安装并配置

<br />

### 安装

	yum -y install mysql mysql-server mysql-devel
	
安装完成..

### 配置

安装完MySQL数据库后，会发现多出了一个mysqld服务，这就是我们的数据库服务，启动它就是启动数据库。

	service mysqld start
	
<img src="http://ozupw8iis.bkt.clouddn.com/11234.png" align="center" class="img-responsive">

查看是否开机启动:

	chkconfig --list | grep mysqld

如果2~5的都是on,说明是开机自动启动，否则如果不是。我们可以设置成开机自动启动:

	chkconfig mysqld on
	
<img src="http://ozupw8iis.bkt.clouddn.com/5.png" align="center" class="img-responsive">

	
## 三.配置数据库密码

先启动mysqld服务，即：service mysqld start，然后执行命令

	mysqladmin -u root -p password 'root'
	
意思是把root用户的密码设置为root，现在我们就可以登录MySQL数据库了 。

<img src="http://ozupw8iis.bkt.clouddn.com/6.png" align="center" class="img-responsive">

其中的datadir是MySQL数据库的存放路径，上面表示本人的数据在CentOS里的/var/lib/mysql目录下。

<img src="http://ozupw8iis.bkt.clouddn.com/11237.png" align="center" class="img-responsive">

上面的mysql和test就是数据库安装后默认的两个数据库文件。

还有一个数据库日志文件，存放位置是：var/log/mysqld.log。

## 四.开启远程连接

刚初始化好的MySQL是不能进行远程登录的.要实现登录的话，强烈建议新建一个权限低一点的用户再进行远程登录。直接使用root用户远程登录有很大的风险。

首先在linux系统中连接mysql:

	mysql -uroot -p（密码）


再创建用户,并设置账号和密码,如下,第一个system表示用户名，%表示所有的电脑都可以连接，也可以设置某个ip地址运行连接，第二个system表示密码:

	GRANT ALL PRIVILEGES ON *.* TO 'system'@'%' IDENTIFIED BY 'system' WITH GRANT OPTION;

执行命令立即生效:

	flush privileges;
	
最后重启一下mysql服务:

	service mysql restart。

## The End