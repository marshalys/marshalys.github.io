---
layout: post
title: "FreeBSD下安装和部署Review Board"
date: 2013-05-20 20:25
comments: true
categories: 技术点滴
tags: [python, freebsd, code review, 开源]
---

Review Board官网：http://www.reviewboard.org

服务器环境：操作系统是FreeBSD 8.2版本

具体步骤：

1、安装Review Board
```
    cd /usr/ports/www/reviewboard
    make install
```
2、安装memcached
```
    cd /usr/ports/databases/memcached
    make install
```
<!-- more -->    
3、安装MySQL Server
```
    cd /usr/ports/databases/mysql55-server
    make install
```    
4、安装nginx
```
    cd /usr/ports/www/nginx
    make install
```
5、创建一个Review Board站点
```
    rb-site install /usr/local/www/reviewboard
```
然后按照提示输入站点相关信息。

6、在nginx配置文件中配置Review Board

在server配置中加入：
```
    root /usr/local/www/reviewboard/htdocs/;

    location /media {
        root /usr/local/www/reviewboard/htdocs;
    }
    location /errordoc {
        root /usr/local/www/reviewboard/htdocs;
    }

    location / {
        # host and port to fastcgi server
        fastcgi_pass 127.0.0.1:3033;
        fastcgi_param PATH_INFO $fastcgi_script_name;
        fastcgi_param REQUEST_METHOD $request_method;
        fastcgi_param QUERY_STRING $query_string;
        fastcgi_param CONTENT_TYPE $content_type;
        fastcgi_param CONTENT_LENGTH $content_length;
        fastcgi_pass_header Authorization;
        fastcgi_intercept_errors off;
        include        fastcgi_params;
    }
```
7、启动Review Board：
```
    rb-site manage /usr/local/www/reviewboard runfcgi method=threaded port=3033 host=127.0.0.1 protocol=fcgi
```
