---
layout: post
title:  "xcodebuild 报错 Command CodeSign failed with a nonzero exit code"
date:   2021-06-17 19:51:10 +0800
categories: iTechnology
---
[Jenkins](https://www.jenkins.io/) 下构建 WebDriverAgent 失败，具体失败原因如下：

```bash
/Users/bachxu/Library/Developer/Xcode/DerivedData/WebDriverAgent/Build/Products/Debug-iphoneos/WebDriverAgentRunner-Runner.app/PlugIns/WebDriverAgentRunner.xctest/Frameworks/WebDriverAgentLib.framework: errSecInternalComponent
Command CodeSign failed with a nonzero exit code
```

出现这个问题的主要原因是由于 Jenkins 以 ssh 方式到 slave 机上，默认是没有账户的，但是运行 xcodebuild 时需要访问钥匙串，**访问钥匙串要求必须有用户身份**。


### 解决方案

在 xcodebuild 执行前添加解锁钥匙串步骤。

```bash
$ security unlock-keychain -p "login pwd" ~/Library/Keychains/login.keychain
```

添加后基本能解决上述问题。需要注意解锁钥匙串命令和运行 xcodebuild 命令需要在同一环境下进行，这是接手 python 作为 Jenkins 桥接项目踩的坑。

在 python 桥接项目中 xcodebuild 命令在子线程中执行，如果在 xcodebuild 前使用子线程执行解锁钥匙串命令，使用 Jenkins 时还是会报 Command CodeSign failed with a nonzero exit code 错误。

如果需要使用子线程执行 xcodebuild，则需要将解锁钥匙串命令在 xcodebuild 子线程中执行才能起到作用。

```python
import subprocess

cmd1 = "security unlock-keychain -p pwd ~/Library/Keychains/login.keychain"
cmd2 = "xcodebuild build-for-testing test-without-building -project {wda_path} -scheme WebDriverAgentRunner -destination id={device_udid} platform=iOS -configuration Debug -resultBundlePath {result_bundle_path}"
cmd = cmd1 + " && " + cmd2

# 以下两种都可以运行
subprocess.Popen(cmd, shell=True)
subprocess.call(cmd,shell=True)
```
