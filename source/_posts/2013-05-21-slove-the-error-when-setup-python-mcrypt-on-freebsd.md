---
layout: post
title: "FreeBSD上安装python-mcrypt出错问题解决"
date: 2013-05-21 15:07
comments: true
categories: 技术点滴
tags: [python, freebds, python-mcrypt, mcrypt]
---

由于需要用到一些加密的东东，打算试一下mcrypt这个库，看到python已经有了一个扩展python-mcrypt，于是打算装上试试。

使用pip安装：
``` sh
    pip install python-mcrypt
```
但是，发现居然安装时报编译错误，仔细看了下错误信息，原来是没有找到mcrypt.h这个文件。但是其实这个文件是存在的，在目录/usr/local/include下，看来这个目录并非编译时查找的默认目录，于是修改了一下python-mcrypt包的setup.py文件，增加文件中Extension类构造函数的如下两个参数：
<!-- more -->
``` py
    include_dirs=["/usr/local/include"],
    library_dirs=["/usr/local/lib"],
```
再手动安装：
``` sh
    python setup.py install
```
就ok了。
