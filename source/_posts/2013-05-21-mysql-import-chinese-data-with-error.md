---
layout: post
title: "mysql导入中文数据乱码问题解决"
date: 2013-05-21 08:56
comments: true
categories: 技术点滴
tags: [mysql]
---

今天在服务器上用mysql导入数据，数据中有中文的，发现导进去是乱码。看了一下帮助，原来默认是使用latin1，所以需要设置一下编码：
```
    # mysql --default-character-set=utf8  -uxxxuser --database=xxxxdb < xxxxdata.sql
```
呵呵，然后就ok了。

另外看了一下，还可以在my.cnf文件中的[client]段添加：
```
    default-character-set=utf8
```
设置，这样可以不用每次导数据都加上--default-character-set=utf8了。
