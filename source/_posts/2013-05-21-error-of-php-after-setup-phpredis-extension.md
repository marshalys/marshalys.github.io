---
layout: post
title: "安装phpredis后PHP出错问题解决"
date: 2013-05-21 15:17
comments: true
categories: 技术点滴
tags: [php]
---

前几天升级了一下虚拟机上PHP的版本，今天发现phpredis这个扩展忘了升级了，于是下载源代码，编译，安装。

运行程序却发现出现了一个错误：
```
    PHP Warning:  PHP Startup: Unable to load dynamic library '/usr/local/lib/php/20100525/redis.so' - /usr/local/lib/php/20100525/redis.so: Undefined symbol "ps_globals" in Unknown on line 0
```
问题很奇怪，在google上找了一会，终于找到了原因，原来是phpredis跟session扩展有一定耦合关系，需要先加载session.so再加载redis.so就没有问题了，于是修改了extensions.ini中各扩展文件顺序，就ok了。
