---
layout: post
title: "discuz模版缓存清理"
date: 2013-05-21 15:51
comments: true
categories: 技术点滴
tags: [php, discuz]
---

同事把论坛换了一台服务器，发现页面内容全乱了，向我求救。于是去看了一下，发现页面输出貌似有php代码，原来discuz用了短标签，于是让同事调整php.ini的设置，允许端标签使用
```
short_open_tag=On
```
修改后重启php，发现页面仍然是乱码。这下郁闷了，只好去看discuz的代码（好乱！）。经过各种尝试，最终发现是模版缓存的原因，清除forumdata/templates目录下文件后刷新页面，终于OK了。
