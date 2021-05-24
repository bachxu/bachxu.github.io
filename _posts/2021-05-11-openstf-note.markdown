---
layout: post
title:  "基于 OpenSTF 的开发之路"
date:   2021-05-11 20:36:12 +0800
categories: iTechnology
---
[OpenSTF](https://openstf.io/)，Smartphone Test Farm 的缩写，是一个开源的 Web 应用程序，用于从浏览器中远程调试智能手机、智能手表等，实现云端使用、调试、测试、管理真机。STF 最初是在 CyberAgent 开发的，用于控制超过 160 多台设备的集合。目前由 [Device Farmer](https://github.com/DeviceFarmer) 进行维护更新。


### STF 的出现解决了哪些问题

#### 测试机高效利用

- 大家都需要设备，需要互相借，还要维护借用表格；
- 大家在不同地区；
- 设备太多，系统版本过多；

#### 提高测试效率

- STF 提供设备的 CPU，内存，电量等性能的监测；
- 对接 Monkey；
- 查看 Log；
- 自动化测试；

#### 测试机器的管理

- 固化测试机器的系统版本，系统设置，不被篡改；
- 追踪查看测试机设置的人员；


### 架构介绍

STF 是通过 [Node.js](https://nodejs.org/en/) + [Angular](https://angular.cn/) 技术实现。

#### 平台语言

- HTML 布局使用 [Pug](https://www.pugjs.cn/api/getting-started.html) 模板引擎，多说一句 Pug 的前身为 jade 模板；
- 前端使用 [Angularjs](https://angular.cn/)，基于 angularjs 1.x 版本；
- 后端使用 [Node.js](https://nodejs.org/en/)，版本为 v8.11.1；
- 数据库使用 [RethinkDB](https://rethinkdb.com/)

#### STF 平台组成

STF 平台主要由：stf, adbkit, STFService.apk, stf-syrup, adbkit-logcat, stf-device-db, adbkit-apkreader, minicap, minitouch, minirev 等组件共同组成。

#### STF 目录结构

- /.tx transifex 网站相关的配置，主要是用于其它语言翻译
- /bin 启动文件，其实是链接到 lib/cli.js
- /conf 应该是 mysql 的配置文件，但是目前没有使用，是个没用的配置
- /doc 一些 STF 平台的说明
- /docker Docker 的相关配置
- /lib 后端代码
- /node_modules npm 相关的组件
- /res 前端 web 代码
- /rethinkdb_data rethinkdb 的数据库文件，建议在 stf 目录下执行 rethinkdb
- /test 检测平台的状态
- /vendor 需要安装到手机上的应用或者服务
- package.json npm 所需要安装的组件集，以 json 格式保存

.... 其它说明文件与打包文件

#### 关于 RethinkDB

Rethinkdb 属于对象型数据库，使用的是 json 字符串格式保存数据，不建议内嵌 json 超过三层。

#### modules 组件

Node 相关的 modules 组件使用的是 npm 的安装方式，而 Package.json 是所要安装的组件名及其版本号。如需要新增组件也可以直接使用 npm install 组件名 @ 版本号。如果安装不成功，大多数是因为网络问题。注意使用 Node 包安装，因为项目依赖有旧版本。

Openstf 相关的 adbkit 等组件有源代码可以修改，底层操作手机也是用 adbkit 这些组件来做的，详细的语法及使用可直接进入相关的 github 官网查看。


### 前端开发

res 目录结构

- /app 前端 web 代码
- /auth 登录认证方式
- /common 语言翻译相关
- /test 登录的跳转与帮助等
- /web_modules web 样式

res 目录分为 service 与 web，service 主要用于后端通讯操作，而 web 用于前端展示。

Service 目录在 res/app/component/stf 目录下。

#### example

比如需要增加一个 task（任务管理的界面），则新建一个 task 文件件，里面创建几个文件：

- index.js
- task-controller.js
- task.pug 等，可能还有样式文件

修改 app 目录下的 app.js,增加 require(‘./task’).name。

增加 task 的 service 到目录 stf/res/app/components/stf/下增加目录 task 文件夹，并创建 index.js 与 taskservice.js，用于通讯。

一般都是使用 get, post 请求，而 stf 使用的是 oboe 模块来接收发送的。代码如下

```javascript
var _ = require('lodash')
var oboe = require('oboe')

module.exports = function TaskServiceFactory($http, socket) {
  var TaskService = {}
  TaskService.reports = new Array()
   oboe('后端定义的api')
   .node('reports.*',function(reports){
        TaskService.reports.push(reports)})
  return TaskService
}
```


### 后端开发

db 是数据操作文件，分为 api.js, index.js, setup.js, tables.js。db 的操作只需要修改 tables.js 与 api.js 即可，其它两个文件可以不用修改。api.js 主要用于数据库增删查操作。

```javascript
dbapi.Name = function(input){
  return db.run(查询语句)
}
```

units 是核心代码，根据其命名可得知其作用。需要什么功能可自定义去实现。

STF 平台已经写了一个方法来显示 Log 的，require 其自带的 logger.js 就可以打日志了，具体使用如下

```javascript
var log = logger.createLogger('xxx模块')
.....
log.info('描述',变量)
log.debug('描述',变量)
.....
```


### 开发环境搭建

1 [RethinkDB](https://rethinkdb.com/) 数据库本地启动，启动主要通过 nohup 指令，nohup 英文全称 no hang up（不挂起），用于在系统后台不挂断地运行命令，退出终端不会影响程序的运行。

```bash
$ nohup rethinkdb --bind all --http-port 8090 &
```

2 启动项目，注意开发环境需要将数据库连接到本地，具体修改在后台相关代码 db 目录下的 index.js 文件，只需修改 url 信息即可。

```javascript
var options = {
    url: process.env.RETHINKDB_PORT_28015_TCP || 'tcp://127.0.0.1:28015'
}
```

3 STF 平台源码编译修改方法，尤其是相关 JS 文件。

```bash
$ gulp clean && gulp webpack:build
```

### 参考

- [8分钟带你了解 OpenSTF](https://segmentfault.com/a/1190000023580853)
- [STF 使用系列心得-改造篇](https://testerhome.com/topics/6117)
- [STF 二次开发辛酸之路](https://testerhome.com/topics/6114)
- [STF 模块解读--websocket](https://testerhome.com/topics/8501)
