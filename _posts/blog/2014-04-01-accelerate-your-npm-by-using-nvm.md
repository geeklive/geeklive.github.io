---
layout: post
title: 使用NVM快速搭建NODE开发环境
description: 本文主要介绍最近自己碰到的几个坑以及搜索到的相应解决方案,比如如何快速搭建Node.js开发环境；使用npm安装一些包速度很慢或者失败；github无法打开或者样式错乱.
category: blog
---

本文主要介绍最近自己碰到的几个坑以及搜索到的相应解决方案：

* 如何快速搭建 [Node.js](http://nodejs.org/) 开发环境.
* 使用 [npm](https://www.npmjs.org/) 安装一些包速度很慢或者失败.
* [github](https://github.com/) 无法打开或者样式错乱.

## 快速搭建 Node.js 开发环境

如果你想长期做 [node](http://nodejs.org/) 开发, 或者想快速更新 [node](http://nodejs.org/) 版本, 或者想快速切换 [node](http://nodejs.org/) 版本,
那么请使用 [nvm](https://github.com/creationix/nvm) 来安装你的 [node](http://nodejs.org/) 开发环境, 保持系统的干净.

### git clone nvm

直接从 github clone nvm 到本地, 这里假设大家都使用`~/git`目录存放 git 项目:

```
$ cd ~/git
$ git clone https://github.com/creationix/nvm.git
```

配置终端启动时自动执行`source ~/git/nvm/nvm.sh`,
在`~/.bashrc, ~/.bash_profile, ~/.profile`, 或者`~/.zshrc`文件添加以下命令:

```
source ~/git/nvm/nvm.sh
```

重新打开你的终端, 输入`nvm`

```
Node Version Manager

Usage:
    nvm help Show this message
    nvm --version Print out the latest released version of nvm
    nvm install [-s] <version> Download and install a <version>, [-s] from source
    nvm uninstall <version> Uninstall a version
    nvm use <version> Modify PATH to use <version>
    nvm run <version> [<args>] Run <version> with <args> as arguments
    nvm current Display currently activated version
    nvm ls List installed versions
    nvm ls <version> List versions matching a given description
    nvm ls-remote List remote versions available for install
    nvm deactivate Undo effects of NVM on current shell
    nvm alias [<pattern>] Show all aliases beginning with <pattern>
    nvm alias <name> <version> Set an alias named <name> pointing to <version>
    nvm unalias <name> Deletes the alias named <name>
    nvm copy-packages <version> Install global NPM packages contained in <version> to current version

Example:
    nvm install v0.10.24 Install a specific version number
    nvm use 0.10 Use the latest available 0.10.x release
    nvm run 0.10.24 myApp.js Run myApp.js using node v0.10.24
    nvm alias default 0.10.24 Set default node version on a shell

Note:
    to remove, delete or uninstall nvm - just remove ~/.nvm, ~/.npm and ~/.bower folders`
```
    
## 使用 npm 安装包失败

由于GFW的强大威慑力，npm 好多安装包很慢或者直接安装error，相关解决方法主要有以下三种：

* 通过config命令.

```
npm config set registry http://registry.cnpmjs.org 
npm info underscore （如果上面配置正确这个命令会有字符串response）
```
* 命令行指定.

```
npm --registry http://registry.cnpmjs.org info underscore 
```
* 编辑`~/.npmrc`加入下面内容.

```
registry = http://registry.cnpmjs.org
```

## 通过 nvm 安装任意版本的 node

nvm 默认是从 <http://nodejs.org/dist/> 下载的, 国外服务器, 必然很慢,
好在 nvm 以及支持从镜像服务器下载包, 于是我们可以方便地从七牛的 node dist 镜像下载:

```
$ NVM_NODEJS_ORG_MIRROR=http://dist.u.qiniudn.com nvm install 0.11.11
```

于是你就会看到一段非常快速进度条:

```
######################################################################## 100.0%
Now using node v0.11.11
```

如果你不想每次都输入环境变量 `NVM_NODEJS_ORG_MIRROR`, 那么我建议你加入到 `.bashrc` 文件中:

```
# nvm
export NVM_NODEJS_ORG_MIRROR=http://dist.u.qiniudn.com
source ~/git/nvm/nvm.sh
```

然后你可以继续非常方便地安装各个版本的 node 了, 你可以查看一下你当前已经安装的版本:

```
$ nvm ls
         nvm
     v0.8.26
    v0.10.26
    v0.11.11
-> v0.11.12
```

## 使用 cnpm 加上 npm

同理 nvm , npm 默认是从国外的源获取和下载包信息, 不慢才奇怪.
可以通过简单的 ·---registry· 参数, 使用国内的镜像 <http://r.cnpmjs.org> :

```
$ npm --registry=http://r.cnpmjs.org install koa
```

于是屏幕又哗啦哗啦地一大片输出:

```
$ npm --registry=http://r.cnpmjs.org install koa
npm http GET http://r.cnpmjs.org/koa
npm http 200 http://r.cnpmjs.org/koa
npm http GET http://r.cnpmjs.org/koa/download/koa-0.5.2.tgz
npm http 200 http://r.cnpmjs.org/koa/download/koa-0.5.2.tgz
npm http GET http://r.cnpmjs.org/escape-html
npm http GET http://r.cnpmjs.org/statuses
...
npm http 200 http://r.cnpmjs.org/negotiator
npm http 200 http://r.cnpmjs.org/keygrip
koa@0.5.2 node_modules/koa
├── koa-compose@2.2.0
├── statuses@1.0.2
├── finished@1.1.1
├── escape-html@1.0.1
├── only@0.0.2
├── debug@0.8.0
├── fresh@0.2.2
├── type-is@1.0.1
├── delegates@0.0.3
├── mime@1.2.11
├── co@3.0.5
├── accepts@1.0.1 (negotiator@0.4.2)
└── cookies@0.4.0 (keygrip@1.0.0)
```

但是毕竟镜像跟官方的 npm 源还是会有一个同步时间差异, 目前 cnpm 的默认同步时间间隔是 15 分钟.
如果你是模块发布者, 或者你想马上同步一个模块, 那么推荐你安装 [cnpm](http://cnpmjs.org/) cli:

```
$ npm --registry=http://r.cnpmjs.org install cnpm -g
```

通过 cnpm 命令行, 你可以快速同步任意模块:

```
$ cnpm sync koa connect mocha
```

呃, 我就是不想安装 cnpm cli 怎么办? 哈哈, 早就想到你会这么懒了, 于是我们还有一个 web 页面:
例如我想马上同步 koa, 直接打开浏览器: <http://cnpmjs.org/sync/koa>
或者你是命令行控, 通过 open 命令打开:

```
$ open http://cnpmjs.org/sync/koa
```

再次要提到 cnpm cli, 它已经默认将 `--registry` 和 `--disturl` 都配置好了, 谁用谁知道.
写到这里, 就更快疑惑那些不想安装 cnpm cli 又吐槽 npm 慢的同学是基于什么考虑不在本地安装一个 cnpm 呢?

## 访问github 好慢或者github样式错乱

好了, 看到这里大家应该对 node 和 npm 已经没有速度慢的问题了.
github 慢, 或者说是它的资源 host 被堵而已, 大家可以通过简单的 hosts 映射解决:

```
185.31.16.184 github.global.ssl.fastly.net
```

## 为何最近访问国外网站这么慢?

听说是 APCN2 又断了: <http://weibo.com/1415338244/ACTYkq8xK>

![problem](http://ww4.sinaimg.cn/large/545c5904gw1eeu7h63hxvj20qs0mp78p.jpg)








