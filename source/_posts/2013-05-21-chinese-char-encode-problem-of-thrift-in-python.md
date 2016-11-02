---
layout: post
title: "python使用thrift中文编码问题解决"
date: 2013-05-21 15:32
comments: true
categories: 技术点滴
tags: [thrift, python, 中文编码]
---

使用thrift（版本0.9.0，客户端、服务器端皆为python），过程中遇到了中文编码问题。string类型的值如果有中文会出现异常。网上查了一下，发现了一些有价值的信息，其中有说在0.9.0时生成代码加上utf8strings选项就可以解决此问题。于是马上去试了一下，发现问题依然存在。决定还是老老实实看一下代码，进过各种折腾，最终还是搞定了这个问题。

原来是因为我用了fastbinary这个扩展，而在0.9.0中这个扩展并没有支持uft8字符串。于是参考了网上一些解决方案的补丁，修改了fastbinary.c文件。用这个代码重新安装python的thrift库后问题解决。
<!-- more -->
最终我是通过加上utf8strings选项同时修改了fastbinary.c的代码来搞定了这个问题。

下面附上修改后的代码片段。

fastbinary.c
``` c
    case T_STRING: {
        PyObject* utf8_str_val = NULL;

         if (value->ob_type == &PyUnicode_Type) {
              utf8_str_val = PyUnicode_AsUTF8String(value);
              if (utf8_str_val == NULL) {
                   return false;
              }
         }

         PyObject* str_val = utf8_str_val == NULL ? value : utf8_str_val;
         Py_ssize_t len = PyString_Size(str_val);

        if (!check_ssize_t_32(len)) {
          return false;
        }

        writeI32(output, (int32_t) len);
        PycStringIO->cwrite(output, PyString_AsString(str_val), (int32_t) len);

         Py_XDECREF(utf8_str_val)
        break;
    }
```
另，参考的网上资料如下，感谢！

https://github.com/originell/thrift

https://issues.apache.org/jira/browse/THRIFT-1460

https://issues.apache.org/jira/browse/THRIFT-395
