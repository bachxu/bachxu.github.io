---
layout: post
title:  "pip 使用国内镜像源"
date:   2021-05-26 20:11:08 +0800
categories: iTechnology
---
[PyPi](https://pypi.org/) 通过 Python 包索引进行查找、安装和发布包文件。安装 Python 包的命令特别简单，我们只需要在终端输入以下命令：

```bash
$ pip3 install xxx # xxx 为包名
```

默认情况下 pip 使用的是国外镜像，在下载的时候可能会出现网络问题，因此我们需要让 pip 使用国内镜像源。

### 国内镜像源

- 清华大学：https://pypi.tuna.tsinghua.edu.cn/simple
- 中国科技大学： https://pypi.mirrors.ustc.edu.cn/simple
- 豆瓣：https://pypi.douban.com/simple
- 阿里云：https://mirrors.aliyun.com/pypi/simple

下文中以清华大学镜像源为默认使用镜像源。


### 针对安装包使用镜像源

我们可以直接在 pip 命令中使用 -i 参数来指定镜像地址，例如：

```bash
$ pip3 install usb -i https://pypi.tuna.tsinghua.edu.cn/simple
```

上述命令只会对当前安装命令有用。


### 全局修改

如果需要全局修改，则需要修改配置文件。 Linux/macOS 环境中，配置文件位置在 **~/.pip/pip.conf**（如果不存在创建该目录和文件）：

```bash
$ mkdir ~/.pip
```

打开配置文件 **~/.pip/pip.conf**，修改如下：

```bash
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn
```

查看镜像地址：

```bash
$ pip3 config list
global.index-url='https://pypi.tuna.tsinghua.edu.cn/simple'
install.trusted-host='https://pypi.tuna.tsinghua.edu.cn'
```

可以看到已经成功修改了镜像。

Windows 下，你需要在当前对用户目录下（C:\Users\xx\pip，xx 表示当前使用对用户，比如张三）创建一个 pip.ini 在 pip.ini 文件中输入以下内容：

```bash
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = pypi.tuna.tsinghua.edu.cn
```
