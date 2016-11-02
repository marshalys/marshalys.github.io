---
layout: post
title: "在Fedora上使用Virtualbox遇到Unable To Find The Sources Of Your Current Linux Kernel问题解决"
date: 2013-05-21 08:51
comments: true
categories: 技术点滴
tags: [fefora, virtualbox]
---

运行Virtualbox提示需要运行/etc/init.d/vboxdrv setup命令，于是就去运行这个命令，结果出错了，错误信息是：Unable To Find The Sources Of Your Current Linux Kernel，查了一些资料发现这个问题主要是由于没有kernel源代码造成的，需要安装kernel的源代码，于是：
```
    # yum install gcc dkms kernel-devel
```
这样安装完成后就好了。

另外需要注意，安装的kernel代码和正在运行的系统kernel是同一个版本的，否则还是会出错。我就遇到了版本不同的问题，新安装的源代码版本比较新，于是只好运行自动升级，把系统kernel的版本更新后就OK了。
