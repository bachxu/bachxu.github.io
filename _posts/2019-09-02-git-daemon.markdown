---
layout: post
title:  "Git Daemon"
date:   2019-09-02 17:18:37 +0800
categories: iTechnology
---
今天主要分享下如何在 macOS 中自动开启 git daemon 服务，以便团队成员之间进行协作开发。macOS 在安装 `CommandLineTools` 完成后就已拥有 git 环境，而我们需要做的是如何自动开启 git daemon 服务。

> git-daemon - A really simple server for Git repositories

### macOS 开机启动配置

- Login Items：macOS 在当前用户登录成功后启动的程序，该类别的启动项配置文件存放在 `~/Library/Prefrences/` 目录下，因此只针对当前登录用户。
- Luanch Daemon：此类型的启动项都有 `launchd` 来负责启动，launchd 是 macOS 下用于初始化系统环境的关键进程，它是内核装载成功后在 OS 环境下启动的第一个进程。
- StartupItems：系统启动过程中的程序，它们可以是运行完就立即终止的程序（比如开机清空废纸篓），也可以是一直持续在系统运行周期的后台进程。

本文中使用 Luanch Daemon 来配置 git daemon 启动服务，采用这种方式来配置自启动项比较简单，只需要创建一个 `plist` 文件，该 plist 文件存放的目录分为以下两类：

1. 系统推荐路径，登录后启动的进程。

```
~/Library/LaunchAgents
/Library/LaunchAgents
/System/Library/LaunchAgents
```

2. 系统启动后立即启动的进程。

```
~/Library/LaunchDaemons
/Library/LaunchDaemons
/System/Library/LaunchDaemons
```


### 配置 git daemon 服务

#### 查找 git 命令路径

{% highlight shell %}
$ find / -name git
{% endhighlight %}

通过查找获得本机 git 环境路径，这边选择的是 `CommandLineTools` 中的环境路径，具体为 /Library/Developer/CommandLineTools/usr/libexec/git-core/git。

#### 创建 plist 文件

在 /Library/LaunchAgents 目录下创建 plist 文件，具体说明如下。

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
        <key>Label</key>
        <string>cc.git.daemon</string>
        <key>UserName</key>
        <string>bach</string>
        <key>OnDemand</key>
        <false/>
        <key>ProgramArguments</key>
        <array>
                <string>/Library/Developer/CommandLineTools/usr/libexec/git-core/git</string>
                <string>daemon</string>
                <string>--base-path=/协作项目路径</string>
                <string>--export-all</string>
        </array>
        <key>KeepAlive</key>
        <true/>
        <key>RunAtLoad</key>
        <true/>
</dict>
</plist>
```

参数说明，其中有部分可能没有出现在上述配置中：

- Label 用来在launchd中的一个唯一标识，类似于每一个程序都有一个identifies一样。
- UserName 指定运行启动项的用户，只有当Launchd 作为 root 用户运行时，此项才适用。
- GroupName 指定运行启动项的组，只有当Launchd 作为 root 用户运行时，此项才适用。
- KeepAlive 这个key值是用来控制可执行文件是持续运行呢，还是满足具体条件之后再启动。默认值为false，也就是说满足具体条件之后才启动。当设置值为ture时，表明无条件的开启可执行文件，并使之保持在整个系统运行周期内。
- RunAtLoad 标识launchd在加载完该项服务之后立即启动路径指定的可执行文件。默认值为false。
- Program 这个值用来指定进程的可执行文件的路径。
- ProgramArguments 如果未指定Program时就必须指定该项，包括可执行文件文件和运行的参数。

通过以下命令可以检查 plist 文件的格式是否正确。

{% highlight shell %}
$ sudo plutil -lint git-daemon.plist
{% endhighlight %}

#### 加载和卸载服务

加载服务

{% highlight shell %}
$ launchctl load git-daemon.plist
{% endhighlight %}

卸载服务

{% highlight shell %}
$ launchctl unload git-daemon.plist
{% endhighlight %}

### 附： Windows 环境下开启服务

网上也有很多关于如何在 Windows 环境下开启 git daemon 服务的教程，这边主要介绍了本人目前使用过的两种方式。

#### 安装 [SourceTree](https://www.sourcetreeapp.com)

通过 SourceTree 打开需要开启 git daemon 服务的协作项目，打开后在软件的工具栏选择【终端】进入 SourceTree 自带的 Shell 窗口，在窗口中输入以下命令开启服务。

{% highlight shell %}
$ git daemon --reuseaddr --base-path=/协作项目目录/ --export-all --verbose --enable=receive-pack
{% endhighlight %}

这样的方式能够灵活开启服务，缺点是在没有脚本的帮助下，每次关掉 SourceTree 或者重启电脑时需要重新进行上述的操作。

#### [SCM-Manager](https://www.scm-manager.org)

SCM Manager 通过 http 来实现版本的管理和分享。框架基于 [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 实现，因此安装软件前须先安装 JDK 环境。SCM Manager 支持插件，如 scm-mail-plugin，scm-ssh-plugin等。关于插件的安装和配置，大家可自行检索相关教程。

Follow your heart.
