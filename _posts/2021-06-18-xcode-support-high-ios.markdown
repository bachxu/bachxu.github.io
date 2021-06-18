---
layout: post
title:  "低版本 Xcode 如何支持高版本 iOS 系统进行真机调试"
date:   2021-06-18 15:04:58 +0800
categories: iTechnology
---
在日常开发或者测试 iOS App 过程中，难免会遇到升级问题，比如 macOS 升级、Xcode 升级以及 iOS 系统升级，升级意味着变数，而变数则会引进问题。macOS、Xcode、iOS 三者版本之间有着依赖关系：

- 当 macOS 版本低于 Xcode 支持版本时，将无法成功安装 Xcode；
- 当 Xcode 支持的 iOS 版本低于真机 iOS 版本时，Xcode 将无法调试、运行、打包 App；

通常情况，当苹果举办发布会推出新系统后，我们都需要升级 macOS、Xcode 以及手机 iOS 系统来支持最新系统，其中随便单独一个系统的升级都需要近个把小时。如果说想减少升级内容，那我们至少也需要升级 Xcode 和手机 iOS 系统。

但是在特殊情况下，比如手上有个紧急的测试任务，需要打包到最新系统的真机进行测试，这时不可能去花几个小时升级 Xcode，那该怎么办呢？

### 解决方案

每次升级 Xcode 后，都会在以下路径有对应的 iPhone 系统的配置包文件。

```bash
/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport
```

该目录下有你当前 Xcode 版本支持的所有真机系统。因此需要处理特殊情况时，只需要拷贝高版本 Xcode 目录下对应的 iPhone 系统版本到低版本 Xcode 目录下即可。

需要注意的是，将配置包文件拷贝过去后，需要将 Xcode 重启再进行编译。如果还不行，可以尝试重启 macOS，如果正常的话，会重新扫描符号文件，待扫描完成就可以进行真机调试了。
