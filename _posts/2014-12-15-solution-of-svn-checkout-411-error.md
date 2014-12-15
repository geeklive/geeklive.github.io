---
layout: post
title: Mac Pro 10.10 SVN无法Checkout
description: Macbook Pro升级Yosemite后svn checkout代码时，遇到411 Content Length Required的报错，本文详细介绍了排查步骤以及解决方法。
category: blog
---


## 一、问题描述
* 问题描述：Macbook Pro升级Yosemite后svn checkout代码时，遇到411 Content Length Required的报错。
* 环境描述：Yosemite系统自带为SVN Version 1.7.17。

## 二、排查步骤
公司服务器SVN为1.6.1版本，由于之前在Win8.1 64bit时候也碰到如此问题：使用Tortoise SVN 1.7以上版本也无法Checkout，故脑中先入为主的认为是由于SVN版本过高原因，故开始了我的漫漫SVN降级路。

### 1、SVN版本问题：使用Homebrew降级SVN 1.7->1.6
什么是Homebrew？
> Homebrew is the easiest and most flexible way to install the UNIX tools Apple didn’t include with OS X.

我们能够通过终端方便的使用它安装管理苹果没有自带的UNIX相关工具软件,详情可参考[Homebrew官网](http://brew.sh/)，Homebrew使用Ruby脚本，而Mac OS X系统自带Ruby，在Terminal里运行以下命令安装：

	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

安装完毕后首先更新formule，确保每次安装的文件都是最新的，`` brew update``，搜索自己需要的软件，例如要搜索svn相关软件；``brew search svn``；安装软件：``brew install svn``。

需要注意的是，目前网上所有资料相关都有提到一个`brew versions`命令，用于查看软件Version，而实际运行结果为：`brew versions svn   Error: Unknown command: versions`，解决方法为`brew versions install`，然后`brew tap homebrew/boneyard`，接下来就可以正常使用brew versions。

	brew versions svn
	Warning: brew-versions is unsupported and will be removed soon.
	You should use the homebrew-versions tap instead:
  	https://github.com/Homebrew/homebrew-versions
	1.8.10   git checkout dca9a4c /usr/local/Library/Formula/subversion.rb
	1.8.9    git checkout 9b75c92 /usr/local/Library/Formula/subversion.rb
	1.8.8    git checkout c6cf8ac /usr/local/Library/Formula/subversion.rb
	1.8.5    git checkout fa4311c /usr/local/Library/Formula/subversion.rb
	1.8.4    git checkout ce669eb /usr/local/Library/Formula/subversion.rb
	1.8.3    git checkout 9b438ce /usr/local/Library/Formula/subversion.rb
	1.8.0    git checkout f56b641 /usr/local/Library/Formula/subversion.rb
	1.8.1    git checkout 55577bb /usr/local/Library/Formula/subversion.rb
	1.7.10   git checkout 0060dc3 /usr/local/Library/Formula/subversion.rb
	1.7.9    git checkout b0e6223 /usr/local/Library/Formula/subversion.rb
	1.7.8    git checkout f7a42d2 /usr/local/Library/Formula/subversion.rb
	1.7.7    git checkout a6dcc41 /usr/local/Library/Formula/subversion.rb
	1.7.6    git checkout 6b8d25f /usr/local/Library/Formula/subversion.rb
	1.7.5    git checkout 5d5cd70 /usr/local/Library/Formula/subversion.rb
	1.7.4    git checkout dc4245c /usr/local/Library/Formula/subversion.rb
	1.7.3    git checkout eb97154 /usr/local/Library/Formula/subversion.rb
	1.7.2    git checkout d89bf83 /usr/local/Library/Formula/subversion.rb
	1.6.17   git checkout 6e2d550 /usr/local/Library/Formula/subversion.rb
	1.6.16   git checkout 83ed494 /usr/local/Library/Formula/subversion.rb

降级成功后发现问题未解决，网上找了几个开源的SVN库测试，均不能svn checkout，此猜想排除（默默吐槽自己浪费的两个小时）。

### 2、检查SVN日志
检查服务器svn日志，发现如下日志：

	9.77.218.146 – - [14/Dec/2014:18:10:49 +0800] “OPTIONS /svn/phpdba HTTP/1.1″ 411 181 “-” “SVN/1.7.17 serf/1.3.3″``
	9.77.218.146 – - [14/Dec/2014:18:12:42 +0800] “OPTIONS /svn/phpdba HTTP/1.1″ 411 18z “-” “SVN/1.7.17 serf/1.3.3″


定位到请求直接被Nginx挡住，返回411错误代码，由此问题确定：Macbook Pro 的svn checkout的时候，没有或者丢失内容长度信息，Nginx检查到缺少内容长度信息，返回411。

## 三、问题解决方法

1. 开启 `Svnserver` 服务，Macbook Pro下面通过svn协议请求代码资源；

2. 修改 `~/.subversion/servers` 文件, 添加 `http-chunked-requests=no` 。

如下：

	[global]
	http-chunked-requests = no

## 四、总结
	
	Svn坑好多，还是Git好用 T_T。


