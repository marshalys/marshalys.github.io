---
layout: post
title: "Fedora上安装KVM虚拟机"
date: 2013-05-20 22:25
comments: true
categories: 技术点滴
tags: [linux, fedora, kvm, 虚拟机]
---

这几天在笔记本上装了Fedora17用于今后开发办公，为了更好的隔离各种开放环境，于是打算安装虚拟机。原来在windows上用的多的是virtualbox，现在在linux上查了一下，貌似KVM比较好，于是打算安装一个试试。下面是安装过程：

首先安装kvm包：
```
    # yum install kvm
```
<!-- more -->
然后安装了几个推荐的用于管理虚拟机的包：
```
    # yum install virt-manager libvirt libvirt-python python-virtinst
```
呵呵，这样就安装成功了，很简单吧！

附上查到的参考文档：http://docs.fedoraproject.org/en-US/Fedora/13/html/Virtualization_Guide/sect-Virtualization-Installing_the_virtualization_packages-Installing_KVM_packages_on_an_existing_Red_Hat_Enterprise_Linux_system.html
