---
layout: post
title: "Flask-SQLAlchemy的SQLALCHEMY_DATABASE_URI中密码有特殊字符的处理办法"
date: 2013-05-21 09:00
comments: true
categories: 技术点滴
tags: [python, sqlalchemy]
---

今天中午，群里面有个哥们问了一个问题，他用的Flask-SQLAlchmey来做数据库访问，而数据库连接串配置项SQLALCHEMY_DATABASE_URI的格式为
```
    mysql://username:password@server/db
```
这种的，由于他们的密码有个特殊字符@，这样如果直接把密码写在连接串的配置项中，就出问题了，连接出错。群里面几个热心人各种提议，未果，于是开始有人说：“密码搞什么特殊字符嘛，把密码改了吧！”。我觉得搞技术的人怎么能是这种心态呢？这也不是什么解决不了的难题，遇到问题就考虑逃避怎么行。虽然这一块我也不是很熟悉，但是这东东貌似开源的，于是决定去查一下。其实这个问题比较简单，查看了一下源代码，大概花了一刻钟，发现原来这个连接串密码可以是通过使用URL编码来处理特殊字符的，所以将密码用URL编码后问题就能解决了。

最后不得不赞一下，开源确实好。
