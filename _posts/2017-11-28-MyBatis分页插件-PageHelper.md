---
layout: post
title: MyBatis分页插件-PageHelper
date: 2017-11-28
categories: blog
tags: [mybatis]
description: MyBatis分页插件-PageHelper
---
# 前言

平时项目中经常需要用到分页,之前使用的是最简单的方法,即在接口中新增pageNum,pageSize入参,每次执行sql时limit,但是每个接口都如此查询,代码冗余且重复。
所以在新项目中，决定使用PageHelper。

## 引入分页插件

### 方法1.引入jar包

下面有各版本jar包

[https://oss.sonatype.org/content/repositories/releases/com/github/pagehelper/pagehelper/](https://oss.sonatype.org/content/repositories/releases/com/github/pagehelper/pagehelper/)
	
[http://repo1.maven.org/maven2/com/github/pagehelper/pagehelper/](http://repo1.maven.org/maven2/com/github/pagehelper/pagehelper/)
	
	
由于使用了sql解析工具,所以还需要下载jsqlparser.jar

[http://repo1.maven.org/maven2/com/github/jsqlparser/jsqlparser/0.9.1/](http://repo1.maven.org/maven2/com/github/jsqlparser/jsqlparser/0.9.1/)
	
[https://gitee.com/free/Mybatis_PageHelper/attach_files](https://gitee.com/free/Mybatis_PageHelper/attach_files)
	
### 方法2.使用maven依赖

```Java
 	<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>4.0.0</version>
	</dependency>
```

这里我使用的是方法2

## 配置

### 方法1.在Mybatis配置xml中配置拦截器插件

```Java
 <!-- 
    plugins在配置文件中的位置必须符合要求，否则会报错，顺序如下:
    properties?, settings?, 
    typeAliases?, typeHandlers?, 
    objectFactory?,objectWrapperFactory?, 
    plugins?, 
    environments?, databaseIdProvider?, mappers?
	-->
	<plugins>
    <!-- com.github.pagehelper为PageHelper类所在包名 -->
    <plugin interceptor="com.github.pagehelper.PageHelper">
        <property name="dialect" value="mysql"/>
        <!-- 该参数默认为false -->
        <!-- 设置为true时，会将RowBounds第一个参数offset当成pageNum页码使用 -->
        <!-- 和startPage中的pageNum效果一样-->
        <property name="offsetAsPageNum" value="true"/>
        <!-- 该参数默认为false -->
        <!-- 设置为true时，使用RowBounds分页会进行count查询 -->
        <property name="rowBoundsWithCount" value="true"/>
        <!-- 设置为true时，如果pageSize=0或者RowBounds.limit = 0就会查询出全部的结果 -->
        <!-- （相当于没有执行分页查询，但是返回结果仍然是Page类型）-->
        <property name="pageSizeZero" value="true"/>
        <!-- 3.3.0版本可用 - 分页参数合理化，默认false禁用 -->
        <!-- 启用合理化时，如果pageNum<1会查询第一页，如果pageNum>pages会查询最后一页 -->
        <!-- 禁用合理化时，如果pageNum<1或pageNum>pages会返回空数据 -->
        <property name="reasonable" value="false"/>
        <!-- 3.5.0版本可用 - 为了支持startPage(Object params)方法 -->
        <!-- 增加了一个`params`参数来配置参数映射，用于从Map或ServletRequest中取值 -->
        <!-- 可以配置pageNum,pageSize,count,pageSizeZero,reasonable,不配置映射的用默认值 -->
        <!-- 不理解该含义的前提下，不要随便复制该配置 -->
        <property name="params" value="pageNum=start;pageSize=limit;"/>
    </plugin>
	</plugins>
```

### 方法2.在spring中配置拦截器

```Java
	 <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<property name="configLocation" value="classpath:mybatis.xml" />
		<property name="mapperLocations">
			<list>
				<value>classpath*:com/zbiti/iepe/points/userManage/dao/*-mapper.xml
				</value>
			</list>
		</property>
		<property name="plugins">
			<array>
				<bean class="com.github.pagehelper.PageHelper">
					<property name="properties">
						<value>
							dialect=mysql
							reasonable=true
						</value>
					</property>
				</bean>
			</array>
		</property>
	</bean>
```
	
这里我用的方法2

## 使用

```Java
	PageHelper.startPage(pageNum, pageSize);
	return taskDao.queryTestTask();
```
	
即在执行sql前先执行startPage即可查询第几页,多少条。紧跟其后的第一个SELECT方法会被分页，后面不会被分页！

分页时，返回的list结果是Page，而Page对象继承自ArrayList，但是如果我们直接返回 ArrayList 的话，比如在 JSON 处理Page 类型的结果时，会被当成 List 来Json 格式化 会丢弃掉 Page 对象的所有扩展属性。

为了保留这些属性，所以将分页Page类型装换成我们自己定义的PageBean, 自己定义个没有继承ArrayList 的PageBean ，包含一个List 的属性来保存分页结果。可以直接使用PageInfo.

如果需要取出分页信息，需要强制转换成Page，在这里我们直接使用PageInfo类对结果进行包装

> List<Map<String,Object>> taskList = taskManageService.queryTestTask(pageNum,pageSize);
	PageInfo<Map<String, Object>> pageInfo = new PageInfo<Map<String, Object>>(taskList);
	
PageInfo有多个属性,常用的有

```Java
	content.getTotal()
	content.getPages()
	content.getPageNum()
	content.getPageSize()
	content.getList()
```

# The End
