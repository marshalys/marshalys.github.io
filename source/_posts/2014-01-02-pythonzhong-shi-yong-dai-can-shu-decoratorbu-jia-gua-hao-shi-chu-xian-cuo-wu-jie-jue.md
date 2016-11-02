---
layout: post
title: "Python中使用带参数Decorator不加括号时出现错误解决"
date: 2014-01-02 20:03
comments: true
categories: 技术点滴
tags: [python, decorator, django]
---

最进使用Django开发网站，有用到ajax的地方，封装了一个Decorator用于将服务器返回数据接口统一为JSON格式，代码类似与：
```python
def json_service(func):
    @wraps(func)
    def _fn(*args, **kwargs):
        result = func(*args, **kwargs)
        content = json.dumps(result, cls=DjangoJSONEncoder)
        return HttpResponse(content, content_type='application/json')
    return _fn
```
这样在view里面可以很简单的如下面这样调用：
```python
@json_service
def logon(request):
    ...

@json_service
def list(request):
    ...
```
这样感觉是挺简介优雅的。不过很快就出现要对`json_service`进行修改的需求了，因为有些地方需要用户登录后才运行进行操作的，因此，这些ajax的接口必须加上权限相关的判断，最简单的就是判断下用户是否登录。
<!-- more -->
于是看了下Django的接口，发现自带一个`login_required`的Decorator，但是是用于页面的，不太适用于这种ajax请求，于是打算改造一下`json_service`增加一个参数用于判断这个ajax接口是否需要判断用户是否登录，*（其实再封装一个新的Decorator也未尝不可，只是觉得那样代码写起来可能有些累赘）*，于是修改了下`json_service`：
```python
def json_service(need_logon=False):
    def decorator(func):
        @wraps(func)
        def _fn(request, *args, **kwargs):
            if need_logon and not request.user.is_authenticated():
                result = fail(_('need logon, please refresh page and retry.'))
            else:
                result = func(request, *args, **kwargs)
            content = json.dumps(result, cls=DjangoJSONEncoder)
            return HttpResponse(content, content_type='application/json')
        return _fn
    return decorator
```
同时将原来view里面的代码稍稍修改：
```python
@json_service(need_logon=True)
def logon(request):
    ...

@json_service
def list(request):
    ...
```
便以为大工告成了。结果，`logon`这个接口一切正常，`list`这个接口确出错了。

其实这个问题，我以前也遇到过一次，后来通过加括号把`@json_service`改为`@json_service()`解决了，这个是跟Decorator本身机制有关，简单说来Decorator的机制可以如下理解*（参考：[pep-0318](http://www.python.org/dev/peps/pep-0318/)）*：
```python
@dec2
@dec1
def func(arg1, arg2, ...):
    pass
```
等价于：
```python
def func(arg1, arg2, ...):
    pass
func = dec2(dec1(func))
```
而
```python
@decomaker(argA, argB, ...)
def func(arg1, arg2, ...):
    pass
```
等价于：
```python
func = decomaker(argA, argB, ...)(func)
```
所以如果不加括号的话就会出问题。

本来觉得加括号就加括号吧，也没啥，但是之前查API时看到Django的那个`login_required`了，貌似人家那个带参数的就可以不带括号写。于是研究了下Django的机制，最后改造了一个可以不带括号写的版本：
```python
def _json_service_decorator(need_logon):
    def decorator(func):
        @wraps(func)
        def _fn(request, *args, **kwargs):
            if need_logon and not request.user.is_authenticated():
                result = fail(_('need logon, please refresh page and retry.'))
            else:
                result = func(request, *args, **kwargs)
            content = json.dumps(result, cls=DjangoJSONEncoder)
            return HttpResponse(content, content_type='application/json')
        return _fn
    return decorator


def json_service(function=None, need_logon=True):
    decorator = _json_service_decorator(need_logon)
    if function:
        return decorator(function)
    return decorator
```
嗯，终于搞定了。

最后不得不感叹两句：
1. Python机制真是挺灵活！
2. 开源就是好！
