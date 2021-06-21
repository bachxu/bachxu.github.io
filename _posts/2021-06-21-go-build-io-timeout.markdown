---
layout: post
title:  "Go build 时报错 i/o timeout"
date:   2021-06-21 15:57:40 +0800
categories: iTechnology
---
在项目中使用 go build 出现 **dial tcp 172.217.24.17:443: i/o timeout** 错误时，应该是网络原因造成。使用 Go 语言进行开发时，会经常依赖各种轮子，所以需要有类似 Proxy，用于快速下载各种 Package。

### Module proxy protocol

Go Modules 是 Golang 官方最近几个版本推出的包管理工具， Go Mod 支持 Module proxy protocol , 配置后用户可以转发请求到代理服务器（但是不支持请求参数），来获取源服务器上的依赖包。

Modules proxy 在处理最近版本包的下载请求， 会先在服务器上找 **@latest** 对应的包。如果没有找到会请求 **<module/@v/list>**，拿到最新预发布的版本号并下载。

同时 proxy protocol 会对请求路径中的 **<module>** 和 **<version>** 的大写字母转换成小写，以解决在大小写文件系统上可能存在的冲突。

即使直接从版本控制系统下载，go 命令也会合成显式信息、mod 和 zip 文件，并将其存储在本地缓存 **$GOPATH/pkg/mod/cache/download** 中, 从 proxy 服务器上下载的包也会放到这个文件夹上。 因为缓存地址和 Proxy URL 下载文件存储地址相同， 通过设置

> GOPROXY=https://example.com/proxy

允许用户访问这些缓存的模板版本。

### Goproxy

[Goproxy](https://github.com/goproxy/goproxy) 是一个基于 Go 的 module proxy protocol 协议实现的一个代理处理器，用于快速接入模块的代理, 使用方法详见官方文档。

同时 Goproxy 官方提供了一个很完整的包代理服务器。

### 解决方案

```bash
$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn,direct
```
