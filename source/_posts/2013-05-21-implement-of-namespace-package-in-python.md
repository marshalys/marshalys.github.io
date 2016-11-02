---
layout: post
title: "python的namespace package实现方式"
date: 2013-05-21 15:25
comments: true
categories: 技术点滴
tags: [python]
---

最近在使用python的过程中，遇到了下面这种情况：

A和B两个模块，我希望有一个共同的命名空间(假设为N)，用如N.A和N.B的方式来使用。注意，A和B是分开部署的（采用setup的方式安装部署），并且不希望通过把代码部署到一起（比如都放在N目录下）来实现这一点。

为了能实现这一点，我进行了一些尝试：

一开始，直接单独建立了两个模块N.A和N.B，然后都安装，发现两个模块只有一个能import成功，另外一个找不到。只要删除掉另外一个，这一个模块就一定可以import成功。反复尝试都是这样，于是我想之前的共用命名空间的想法是实现不了了。
<!-- more -->
后来，突然想到，flask里面的ext貌似就是这个样子的，那些插件都是单独部署，且都在flask.ext这个命名空间下。于是立马去看了看flask的实现方式，发现他采用了一种import hook的机制来实现的（该机制请见PEP 302 http://www.python.org/dev/peps/pep-0302/ ），具体的过程就是在`import flask.ext`这个模块的时候，调整了这个模块的import的过程，会去搜索使用了`flask_`或者`flaskext`前缀模块，比如`import flask.ext.sqlalchemy`实际会去`import flask_sqlalchemy`这个模块。于是我去实验了一下这种方式，确实可行，于是打算采用这种方式去组织代码结构了。只是觉得这样一个模块有两种import方式，稍微有些不舒服。

这天，在看setuptools的文档，突然发现他有一个叫namespace_packages的参数，我突然意识到这可能会跟我想要的那种共用命名空间的想法有关系，于是顺藤摸瓜，发现了这个：PEP 382 -- Namespace Packages（ http://www.python.org/dev/peps/pep-0382/ ），呵呵，我的这个需求原来早就有人有了，最终决定采用如下方式：

建立一个单独的N模块，在其__init__.py中放入如下代码：
``` py
from pkgutil import extend_path
__path__ = extend_path(__path__, __name__)
```
