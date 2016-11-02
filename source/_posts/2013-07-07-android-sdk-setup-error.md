---
layout: post
title: "Android SDK安装"
date: 2013-07-07 18:29
comments: true
categories: 技术点滴
tags: android
---

今天尝试了一下安装android sdk，发现还是挺折腾的，于是记录一下：

首先在[android SDK页面](http://developer.android.com/sdk/index.html)页面下在sdk包（我选的是不跟ADT绑定的）。这个过程还挺顺利，很快就把100多M的压缩包下载好了。

然后，我打开我的idea12，打算新建一个android工程试试，发现需要指定sdk目录，于是设置好，却发现指向失败了，提示"can't find any android targets"，于是查了一些资料，发现自己目前完全没有搞清楚这个sdk是怎么用的：下载完这个基本的sdk压缩包其实只是很少的一部分，后续还需要安装各种安卓版本相关的包，需要通过tools/android来进行管理。
<!-- more -->

需要下载的东西好多啊，在经历了n次网络超时错误后（主要是由于GFW的干扰），终于貌似把相关的包都安装上了。

于是赶紧去运行一个例子试试，悲剧的发现在运行adb时还有错误：
```
	/lib/ld-linux.so.2: bad ELF interpreter: No such file or directory
```
还好有google，搜了一下，貌似由于我电脑是64位系统造成的，解决方案：
```
	sudo yum install libstdc++.i686
	sudo yum install ncurses-libs.i686
	sudo yum install zlib.i686
```
于是就OK了。
接下来可以试试Android开发啦:)
