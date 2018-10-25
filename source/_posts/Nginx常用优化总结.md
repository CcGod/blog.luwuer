---
title: Nginx 常用优化总结
tags: [Nginx]
date: 2017-09-19 17:40:50
---

# Nginx常用优化总结

## 基础匹配规则

语法：
```bash
# 匹配优先级排序

=  # 进行普通字符精确匹配
^~ # 表示普通字符匹配，如果该选项匹配，则不再匹配别的选项，一般用来匹配目录
~  # 波浪线表示执行一个正则匹配，区分大小写
~* # 表示执行一个正则匹配，不区分大小写
@  # 定义一个命名的location，使用在内部定向时，例如 error_page, try_files
```

示例：
```bash
location  = / { 
  # 只匹配"/"
}

location ^~ /images/ {
  # 匹配任何以 /images/ 开始的请求，匹配成功则不再匹配其它location
}

location / { 
  # 匹配任何请求，因为所有请求都是以"/"开始
  # 但是更长字符匹配或者正则表达式匹配会优先匹配
}

location ~* .(gif|jpg|jpeg)$ {
  # 匹配以 gif / jpg / jpeg 结尾的请求
}
```


## 1. expires缓存调优

主要针对图片视频等占用带宽的文件

```bash
location ~* \.(jpeg|png|gif|jpg)$ { # 匹配任何以 .jpeg / .png / .gif / .jpg 结尾的图片

   expires 7d; # 缓存7天

   log_not_found off; # 是否在error.log中记录不存在的错误, 默认是

   access_log off; # 是否在access.log中记录
}   
```


## 2. 防盗链

防止有人直接使用自己网站中的图片\视频链接，消耗服务器宽带资源

```bash
location ~* ^/images/.+\.(jpg|gif|png|swf|flv|wma|wmv|asf|mp3|mmf|zip|rar)$ {

    # 有效的请求来源: 没有来源（none）、有来源但被防火墙或代理去除了、ww..
    valid_referers none blocked www.luwuer.com luwuer.com; 

    if ($invalid_referer) { # 非法的请求来源

      # rewrite www.baidu.com

       return 404;

       break;
     }

     access_log off;
 }
```


## 3. gzip调优
使用gzip压缩功能，可能为我们节约带宽，加快传输速度。

Nginx启用压缩功能需要你来ngx_http_gzip_module模块， 一般我们需要压缩的内容有：文本、js、html、css。

gzip需要消耗CPU计算资源。

```bash
http {
  # ...

  gzip on; # 开启压缩功能

  gzip_min_length 1k; # 允许压缩的（页面、文件、请求）最小字节数，小于1k或得不偿失

  gzip_buffers 4 32k; # 压缩缓冲区大小，表示申请4个单位为32K的内存作为压缩结果流缓存

  gzip_http_version 1.1; # 压缩版本，用于设置识别http版本

  gzip_comp_level 6; # 压缩比例，1压缩比例最小、消耗cpu资源也少，9最大...

  # 压缩请求类型，默认text/html
  gzip_types text/plain text/css text/javascript application/json application/javascript application/x-javascript application/xml;

  gzip_vary on; # vary header支持，让浏览器可以缓存经过gzip压缩的页面

  # 作为反向代理服务器时启用，决定是否对代理请求的应答启用gzip，any表示无条件进行压缩，其他选项不做列举
  gzip_proxied any; 

  # server {}
}

```

## 开启高效传输模式

> 如果图片显示不正常，把sendfile修改为off

```bash
http {
  # ...

  # 开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载
  sendfile on; 

  # 必须在sendfile开启模式才有效，防止网路阻塞，积极的减少网络报文段的数量（告诉nginx在一个数据包里发送所有头文件，而不一个接一个的发送）
  tcp_nopush on;

  # server {}
}
```

## 连接超时时间

主要目的是保护服务器资源，CPU，内存，控制连接数，因为建立连接也是需要消耗资源的

```bash
  keepalive_timeout 60; # 客户端连接保持会话超时时间，超过这个时间，服务器断开这个链接
  
```

> 更多参见[这里](https://blog.csdn.net/Kangshuo2471781030/article/details/79198716)