---
layout: post
title:  "macOS 上如何搭建 python3 环境"
date:   2020-10-29 16:43:30 +0800
categories: iTechnology
---
由于 Python 2.x 版本存在编码等诸多问题，Python 3 问世后，官方决定在 2020 年 1 月 1 日开始不再对 Python 2 版本进行维护，因此我们需要在 macOS 上搭建 python3 环境。

### 安装 Python3

安装 Python3 的途径有很多种，比如通过 macOS 相关的包管理器，这边主要介绍通过官网下载 pkg 安装包进行安装。登录[ Python 官网](https://www.python.org/) 进行最新版本的下载，现在后双击文件按照指示进行安装。

安装完成后，在终端通过输入 `python3` 即可进入 python3 环境以及查看当前 python 的版本号。

通过 pkg 安装的 python3 在 macOS 的 `/Library/Frameworks/Python.framework/` 目录下。

### 构建 Python3 的虚拟环境

为了不影响系统的目录结构，达到隔离第三方库，习惯在项目中构建虚拟的 python 环境，这样每个项目使用的依赖库只会存在当前的虚拟环境中，互不影响。

1. 使用 python3 中的 pip3 安装最新版本的 virtualenv，pip3 指令在 `/Library/Frameworks/Python.framework/Versions/3.9/bin` 目录下，但是可以直接使用。

``` shell
$ pip3 install virtualenv
```

2. 成功安装 virtualenv 后，在 `/Library/Frameworks/Python.framework/Versions/3.9/bin` 目录下多了 `virtualenv` 指令文件，通过该指令在相应的项目目录下创建 python3 虚拟环境。

``` shell
$ /Library/Frameworks/Python.framework/Versions/3.9/bin/virtualenv -p python3 py3env
```

3. 成功搭建虚拟环境后，即可激活。

``` shell
$ source ./py3env/bin/activate
```

4. 激活后，通过 python 指令即可查看当前虚拟环境的 python 版本号，所有安装的依赖库也都会安装在该虚拟环境下。

Follow your heart.
