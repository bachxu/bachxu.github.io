---
layout: post
title:  "简单认识 Memcached"
date:   2015-01-27 11:15:08 +0800
categories: iTechnology
---
Memcached 是什么？[Memcached](http://memcached.org/) 是一款免费开源的高性能分布式内存对象缓存系统，本质上是通用的，主要目的是通过减轻数据库负载来加快动态网络应用。Memcached 是一个内存键值存储系统，用于存储来自数据库调用、API 调用或页面渲染结果的小块任意数据（字符串、对象）。

Memcached 简单而强大。它简单的设计促进了快速部署，易于开发，并解决了大型数据缓存所面临的许多问题。其 API 可用于大多数流行的语言。

### 服务端

#### 安装方式

第一种是通过下载上传源文件，通过 make 指令来进行安装。第二种方式是通过 apt-get 方式进行安装，具体操作如下：

```shell
$ sudo apt-get install memcached
```

#### 启动服务

Memcached 服务的启动指令如下：

```sehll
$ sudo memcached -d -m 64 -p 11211 -l 127.0.0.1 -u user -c 1024
```

启动参数描述：

- -d : 启动一个守护进程
- -m : 分配给Memcache使用的内存数量，单位是MB，默认是64MB
- -u : 运行Memcache的用户
- -l : 监听的服务器IP地址
- -p : 设置Memcache监听的端口，默认是11211
- -c : 设置最大并发连接数，默认是1024
- -P : 设置保存Memcache的pid文件
- -h : 显示帮助

#### 修改配置

通过找到以下文件，并对其编辑，可修改 Memcached 的配置项

```shell
$ sudo vim /etc/memcached.conf
```

### 客户端

客户端这边选择了 Python 进行开发相应的应用。首先需要安装 Memcached 客户端包 **python-memcached**。

具体使用示例

```python
# cache_controller 模块
import memcache
from tg import config

__mc__ = memcache.Client([config.get('memcache.addr')])

def get(key):
    return __mc__.get(key.encode('ascii'))

def set(key, value):
    return __mc__.set(key.encode('ascii'), value)

def delete(key):
    return __mc__.delete(key.encode('ascii'))
```

具体调用如下

```python
import cache_controller as mc

mc.set(key,value)
mc.get(key)
mc.delete(key)
```
