---
layout: post
title: 根据域名查找项目程序位置
description: 当我们维护多台服务器的上的N多项目时候，突然某个项目出bug了，此时项目程序在哪台服务器这个问题经常会困扰我们，本篇主要讨论根据域名迅速定位项目程序的几种方法。
category: blog
---
## 问题描述

当我们维护多台服务器的上的N多项目时候，突然某个项目出bug了，此时项目程序在哪台服务器这个问题经常会困扰我们，本篇主要讨论根据域名迅速定位项目程序的几种方法。

## 问题解决方法

### 一、查询dns最终解析结果
<ul>
	<li>1、上任意一台linux服务器</li>
	<li>2、从根服务器开始追踪一个域名的解析过程： `dig xxx.com +trace|less`</li>

   	ps:查询出来每列的数据：域名——缓存时间——dns类型——IP或者根域名
    	<li>3、查看最后的A记录，即dns的最终解析到的服务器地址（可能会有多个，需要多次执行后续操作才可得到完整的路由情况）</li>

    	ps:也可以用`nslookup`方法，比较简易，好处是在windows下也可用.
</ul>

### 二、登陆dns解析获得的相应linux服务器，查看内部路由情况
<ul>
	<li>1、访问的时候默认访问的是80端口</li>
	<li>2、查找80端口对应的服务软件（squid、nginx、apache）</li>
	       root下可以使用netstat -tupln|grep :80或者lsof -i:80查看是是哪个服务监听的80端口
	       ci123dev下，分别查看squid、nginx、apache，看lister监听的是否80端口

	<li>3、查看相应配置文件，找到对应域名的路由情况</li>
		```
		/opt/ci123/squid/etc/squid.conf
		/opt/ci123/nginx/conf/vhost/*.com
		/opt/ci123/apache/conf/httpd.conf
		
		```
	<li>4、配置文件如何查看</li>
		squid 配置文件，直接搜索到对应域名，看上一行，看其对应的ip以及端口
		nginx 配置文件，查看其相应的location，看代理到个服务器的什么端口（注意看静态文件的缓存目录，修改前端的用）
		apache 配置文件，找相应的DocumentRoot 以及Rewrite
</ul>

### 三、php程序的rewrite
<ul>
	<li>1、根据apache Rewrite 的结果，找到相应的php接受文件.</li>
	<li>2、查看该php文件，看其正则匹配情况，再定位相应的程序位置.</li>
</ul>


