---
layout: post
title:  "Mongodb 安装及使用"
date:   2015-03-06 15:33:50 +0800
categories: iTechnology
---
[MongoDB](https://www.mongodb.com/) 是一个通用的、基于文档的分布式数据库，是为现代应用开发者和云时代而构建的。下面主要分享下 MongoDB 的安装方式以及简单的使用方式。

### 安装

第一种安装方式是通过 Linux 的 apt-get 进行安装，安装命令如下

```shell
$ sudo apt-get install mongodb
```

第二种是通过手动方式进行安装，操作相对繁琐些。

1 导入包管理系统使用的公钥。

```shell
$ wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
```

2 为 MongoDB 创建一个列表文件。

```shell
$ echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
```

3 重新加载本地软件包数据库。

```shell
$ sudo apt-get update
```

4 安装MongoDB包。

```shell
$ sudo apt-get install -y mongodb-org
```

### 修改配置文件

可以通过修改以下文件，修改 MongoDB 的配置项。

```shell
$ sudo vim /etc/mongodb.conf
```

### MongoDB 简单操作

导出数据

```shell
$ mongoexport --db --collection collection --out collection.json
```

导入数据

```shell
$ mongoimport --db --collection collection --file collection.json
```
