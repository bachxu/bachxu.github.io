---
layout: post
title:  "在 TurboGears 中实现文件上传"
date:   2018-03-13 16:10:14 +0800
categories: iTechnology
---
最近在项目中需要实现将本地资源上传到服务器的功能，客户端通过发起一个 HTTP POST 请求，其中 **content-type** 为 **multipart/form-data**。本篇文章主要讲解了如何在 [TurboGears](http://turbogears.org) 框架下实现服务端对应的功能。

> TurboGears 是一个提供网页前端到后端整合的网站框架，使用 Python 动态语言。

### 暴露 API 接口

在 root.py 控制器文件中添加资源上传到服务器的 API 接口，以供客户端发起网络请求时调用。

``` python
@expose()
def upload(self, *args, **kw):
    """处理本地资源上传"""
```

### 创建服务端资源目录

由于创建服务端资源目录需要对文件等进行操作，因此需要导入 os Python 系统包。

``` python
import os
```

定义服务端管理本地上传资源的文件目录，如果服务端不存在该目录则创建该目录。

``` python
upload_dir = os.path.abspath('相对管理资源文件目录')
if not os.path.exists(upload_dir):
    os.makedirs(upload_dir)
```

### 二进制内容读取并保存

读取客户端上传资源文件的二进制内容，并保存到服务端管理资源的文件目录下。

``` python
data = kw['file'].file.read()
target_file_name = os.path.join(upload_dir, '文件名称') + '文件格式'
f = open(target_file_name, 'wb')
f.write(data)
f.close()
```

### 补充

在将本地资源文件保存到服务端后，可以根据项目的具体情况集合数据库操作进行使用。

Follow your heart.
