---
layout: post
title: 关于下载速度突破物理带宽的研究
description: 近日使用迅雷会员使用高速通道以及开通蓝钻后下载英雄联盟时候发现，下载速度突破物理带宽限制，本人8M电信宽带而下载速度达到2.5~3M/s，针对该现象本人做了一些功课，记录如下。
category: blog
---
## 问题描述
近日使用迅雷会员使用高速通道以及开通蓝钻后下载英雄联盟时候发现，开通VIP后下载速度突破物理带宽限制，本人8M电信宽带而下载速度达到2.5~3M/s，这与计算机网络所学相悖，下午花费一段时间做了一些功课，记录如下。

## 带宽与下载速度的关系
有很多人以为2Mb的带宽等同於2MB/s下载速度，以致出现不少误会，其实一般数据机及网络通讯的传输速率都是以「bps」为单位。如28Kbps、56Kbps、128Kbps、1Mbps 及 10Mbps 等等。小写 b 代表 bit(位元),大写 B代表  Byte(字节),1Byte=8bit。以2M 带宽为例，2Mbps 等如2 X 1024/8，亦即等于256KB/s的下载速度，但以上的速度只是理论上的最高速度。因为在实际应用上要减去大约30% 的Ethernet header、TCP header及 ATM header等网络损耗，所以其平均下载速度约为180KB/s 左右。 一般说来，带宽越大，下载速度就越快，但下载速度除受服务供应商因素所影响外，用户本身所使用操作系统、浏览器、电脑配备等亦可影响下载速度，而且还跟你连接的网站的服务器能力，提供的带宽有密切关系。（同时浏览同个网站的人数越多，这个网站所能提供给每个浏览者的带宽就越低）。这些都影响下载速度。

网速则指服务器响应你的连接要求或交换你发送的数据的时间，也就是网络延时或者叫ping值，单位是ms（毫秒），不少用户把网速等同于下载速度，也是不正确的。Ping值的大小表示是客户端和服务器端交换数据的快慢。Ping值越小，网络速度越好。 一般说来，带宽越大，ping值就会越小，但实际情况却取决于网络线路的连接和路由，出现相反的情况也不足为奇。 

综合以上，物理带宽8M的情况下，理论下载速度极限是1M/s，实际由于一些网络损耗，大概在850~950K/s左右。

## 下载加速的原理揣测
个人了解到，目前的下载加速主要有以下三类：

* CDN加速，通过CDN将文件分发到全国各地不同机房的服务器里边，在你下载的时候选取离你最近或者与你传输速度最快的一个或者多个服务器。
* P2P加速，一般使用旋风、迅雷等下载工具进行下载的时候会使用到这种技术。基本原理是搜索P2P网络中不同位置相同资源，然后同时下载资源。但是目前宽带运营商提供的宽带的上传带宽只有512K或者稍微多一点，同时还有很多人有下载完后改名、转移文件位置、关闭下载工具、限制上传速度等陋习速度可能要慢一点。
* 还有一种P2SP，相比P2P增加了一个S就是服务器，在搜索P2P网络中同时搜索服务器以及镜像服务器。因为服务器所在机房的上传带宽远大于个人用户的上传带宽，同时可以从多个服务器下载，很轻松就能达到宽带的最大速率。

个人理解QQ蓝钻游戏下载加速主要利用了第一类和第三类。

由于下载速度受本地网络、运营商差异、下载文件资源、同时下载的任务数等多方面因素影响，迅雷以及据我体验的QQ旋风通过多线程下载技术使客户下载速度远远高于浏览器下载。

对于付费会员，厂家投入巨额资金，购了额外的带宽，建设了额外数据中心，加大网络建设，因此会员的下载速度比非会员快是因为他们付费为自己购买了更多的带宽与服务器，拥有了专属的高速下载通道，如以下示意图。

<p class="picture"><img alt="" src="http://daiweiyang.qiniudn.com/xunlei.jpg"/></p>

因此，对今天碰到的蓝钻下载加速突破物理带宽揣测原因为：通过CDN将文件分发到江苏南京电信机房的服务器里，并分发到与本机网络所处同一个城域网的环境，这样造成下载速度走局域网资源从而突破物理带宽。

以上。大家若有异议或补充欢迎留言讨论。
