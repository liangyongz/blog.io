---
layout: post
title: Apache与Nginx对比
date: 2017-11-23
categories: blog
tags: [nginx，apache]
description: Apache与Nginx对比
---
## 一、Nginx概述
		Nginx (发音为[engine x])专为性能优化而开发，其最知名的优点是它的稳定性和低系统资源消耗，以及对并发连接的高处理能力(单台物理服务器可支持30000～50000个并发连接)， 是一个高性能的 HTTP 和反向代理服务器，也是一个IMAP/POP3/SMTP 代理服。

## 二、Apache服务器和nginx的优缺点
		我们之前大量使用Apache来作为HTTPServer。Apache具有很优秀的性能，而且通过模块可以提供各种丰富的功能。
		
### 1)首先Apache对客户端的响应是支持并发的，运行httpd这个daemon进程之后，它会同时产生多个子进程/线程，每个子进程/线程分别对客户端的请求进行响应；

Apache两种工作模式:是prefork模式与worker模式

prefork每个子进程只有一个线程，效率高但消耗内存大，是lunix下默认的模式；worker模式每个子进程有多个线程，内存消耗低，但一个线程崩溃会牵连其它同子进程的线程。

# 2)另外，Apache可以提供静态和动态的服务，例如对于PHP的解析不是通过性能较差的CGI实现的而是通过支持PHP的模块来实现的(通常为mod_php5，或者叫做apxs2)。

3)缺点:

因此通常称为Apache的这种Server为process-based server，也就是基于多进程的HTTPServer，因为它需要对每个用户请求创建一个子进程/线程进行响应；

这样的缺点是，如果并发的请求非常多(这在大型门户网站是很常见的)就会需要非常多的线程，从而占用极多的系统资源CPU和内存。因此对于并发处理不是Apache的强项。

4)解决方法：

目前来说出现了另一种WebServer，在并发方面表现更加优越，叫做asynchronousservers异步服务器。最有名的为Nginx和Lighttpd。所谓的异步服务器是事件驱动程序模式的event-driven，除了用户的并发请求通常只需要一个单一的或者几个线程。因此占用系统资源就非常少。这几种又被称为lightweight web server。举例，对于10,000的并发连接请求，nginx可能仅仅使用几M的内存；而Apache可能需要使用几百M的内存资源。

使用Apache来作为HTTPServer的情况我这里不再多做介绍；上面我们介绍到Apache对于PHP等服务器端脚本的支持是通过自己的模块来实现的，而且性能优越。

我们同样可以使用nginx或者lighttpd来作为HTTPServer来使用。

nginx和Apache类似都通过各种模块可以对服务器的功能进行丰富的扩展，同样都是通过conf配置文件对各种选项进行配置。对于PHP等，nginx没有内置的模块来对PHP进行支持，而是通过FastCGI来支持的。

nginx则没有自己提供处理PHP的功能，需要通过第三方的模块来提供对PHP进行FastCGI方式的集成。













