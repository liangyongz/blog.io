---
layout: post
title: LinuxCentOS下安装Nginx
date: 2017-11-22
categories: blog
tags: [linux,nginx]
description: nginx的安装
---
<h2 id="toc_0">安装所需环境</h2>

<h3 id="toc_1">一.gcc安装</h3>
<p>安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境，如果没有 gcc 环境，则需要安装：</p>
<pre><code class="language-none">yum install gcc-c++</code></pre>
<br />

<h3 id="toc_1">二. PCRE pcre-devel 安装</h3>
<p>PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。nginx也需要此库。命令：</p>
<pre><code class="language-none">yum install -y pcre pcre-devel</code></pre>
<br />


<h3 id="toc_1">三. zlib 安装</h3>
<p>zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip ，所以需要在 Centos 上安装 zlib 库。</p>
<pre><code class="language-none">yum install -y zlib zlib-devel</code></pre>
<br />

<h3 id="toc_1">四. OpenSSL 安装</h3>
<p>OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及 SSL 协议，并提供丰富的应用程序供测试或其它目的使用。
nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。</p>
<p></p>
<pre><code class="language-none">yum install -y openssl openssl-devel</code></pre>
<br />

<h2 id="toc_0">安装配置</h2>

<h3 id="toc_1">一.使用wget命令下载</h3>
<p>直接使用wget命令下载：</p>
<pre><code class="language-none">wget -c https://nginx.org/download/nginx-1.10.1.tar.gz</code></pre>
<br />

<h3 id="toc_1">二.解压</h3>
<pre><code class="language-none">tar -zxvf nginx-1.10.1.tar.gz</code></pre>
<pre><code class="language-none">cd nginx-1.10.1</code></pre>
<br />

<h3 id="toc_1">三.配置</h3>
<p>推荐直接使用默认配置</p>
<pre><code class="language-none">./configure</code></pre>
<br />

<h3 id="toc_1">四.编译安装</h3>
<pre><code class="language-none">make</code></pre>
<pre><code class="language-none">make install</code></pre>
<p>查找安装路径:</p>
<pre><code class="language-none">whereis nginx</code></pre>
<br />

<h3 id="toc_1">五.启动停止Nginx</h3>
<pre><code class="language-none">cd /usr/local/nginx/sbin/</code></pre>
<p>启动:</p>
<pre><code class="language-none">./nginx</code></pre>
<p>此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程:</p>
<pre><code class="language-none">./nginx -s stop</code></pre>
<p>此方式停止步骤是待nginx进程处理任务完毕进行停止:</p>
<pre><code class="language-none">./nginx -s quit</code></pre>
<p>重新加载配置文件:nginx的配置文件 nginx.conf 修改后，要想让配置生效需要重启nginx，使用-s reload不用先停止nginx再启动nginx即可将配置信息在nginx中生效，如下:</p>
<pre><code class="language-none">./nginx -s reload</code></pre>
<p>查询nginx进程：</p>
<pre><code class="language-none">ps aux|grep nginx</code></pre>
<br />

<h3 id="toc_1">六.开机自启动Nginx</h3>
<p>在rc.local增加启动代码就可以了</p>
<pre><code class="language-none">vi /etc/rc.local</code></pre>
<p>增加一行:</p>
<pre><code class="language-none">/usr/local/nginx/sbin/nginx</code></pre>
<img src="http://ozupw8iis.bkt.clouddn.com/3d294052472e8971134d5888aaf1cbf.png" align="center" class="img-responsive">
<p>设置执行权限：</p>
<pre><code class="language-none">chmod 755 rc.local</code></pre>
<br />

<h3 id="toc_1">Nginx安装完毕</h3>














