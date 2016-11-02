---
layout: post
title: "Fedora 17系统双显卡笔记本中关闭不用的独显的简单方法"
date: 2013-05-21 15:01
comments: true
categories: 技术点滴
tags: [fedora, linux, 双显卡]
---

在终端中输入：
``` sh
    cat /sys/kernel/debug/vgaswitcheroo/switch
```
输出如下：
```
    0:DIS: :Pwr:0000:01:00.0
    1:IGD:+:Pwr:0000:00:02.0
```
其中“IGD”表示集成显卡，“DIS”表示独立显卡；加号（“+”）表示当前用作输出（或称“连接上”（connected））的显卡；“Pwr”代表正在供电，“Off”代表已关闭。如果看到两个显卡都显示“Pwr”，则说明都在消耗着电能。
<!-- more -->
临时关闭某一显卡（重启后失效）：
``` sh
    su
    echo OFF > /sys/kernel/debug/vgaswitcheroo/switch
```
这时再看以下显卡的运行情况：
```
    0:DIS: :Off:0000:01:00.0
    1:IGD:+:Pwr:0000:00:02.0
```
呵呵，可以看到没有被使用的独立显卡电源已经被关闭了。

另外切换显卡也可用此法，将OFF替换为IGD或者DIS就可。
