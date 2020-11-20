---
layout: post
title:  如何创建私有 CocoaPods 仓库
date:   2020-11-20 15:19:40 +0800
categories: iTechnology
---
[CocoaPods](https://cocoapods.org/) 是一个 Swift 和 Objective-C Cocoa 项目的依赖管理器。它拥有超过 7.8 万个库，并被用于超过 300 万个应用程序。CocoaPods 可以优雅地扩展项目。在公司实际开发过程中，可能会用到私有的 CocoaPods 仓库来管理内部的公共库。


### 1、创建一个私用的 Spec Repo

在 Git 服务器上创建一个 Repo，如果是针对公司内部使用的话可以创建私有的仓库，推荐使用 [GitHub](https://github.com/)、[GitLab](https://about.gitlab.com/)。我们在 GitHub 上创建一个空的私有仓库，命名为 BBSpecs，这个仓库是用来存放内部所有的私有库的 spec 文件，就跟 https://github.com/CocoaPods/Specs 是用来存放所有官方的 specs 文件一样。

创建完私有的仓库后，我们需要在本地终端执行以下命令：

``` shell
$ pod repo add REPO_NAME SOURCE_URL
```

其中的 REPO_NAME 是我们要添加的私有库名称，SOURCE_URL 是仓库的 git 地址。这里做的其实是创建的工作，也就是在 **~/.cocoapods/repo** 目录下添加了一个以你的私有库为名的文件夹，但是并没有添加 spec 文件。

需要注意的是如果空仓库没有任何提交，可以创建一个 LICENSE 以及 README 文件来进行初始化提交，以避免后期依赖的私有库提交时报错。


### 2、添加私有的 Repo 安装到你的 CocoaPods 中

首先还是需要在 Git 服务器上创建一个空的私有 Repo 用来作为私有依赖库项目工程地址，通过本地终端进行 .podspec 文件创建。

``` shell
$ pod spec create BBCustomLib
```

需要注意的是每个 Pods 依赖库必须有且仅有一个名称和依赖库名保持一致。打开 .podspec 文件：

``` ruby
Pod::Spec.new do |spec|
  # 库名
  spec.name         = "BBCustomLib"
  # 版本号
  spec.version      = "0.0.1"
  # 对库的类型、作用做一个简短的描述
  spec.summary      = "BBCustomLib for bach"
  # 库工程目录地址，这里会根据你在 SVN 或 Git 服务器上创建项目的地址自动生成
  spec.homepage     = "http://xxx/BBCustomLib"
  # 遵守的哪些许可协议，默认就行了
  spec.license      = { :type => "MIT", :file => "LICENSE" }
  # 作者信息
  spec.author             = { "bach" => "bach.xu@outlook.com" }
  # 最低兼容的iOS版本号
  spec.platform     = :ios, "9.0"

  spec.source       = { :git => "https://github.xxx/bach/BBCustomLib.git", :tag => "#{spec.version}" }

  # 依赖的第三方 Framework。多个可以使用英文逗号隔开
  spec.vendored_frameworks = "BBCustomLib/Classes/BBCustomLib.framework"
  # 依赖的资源文件
  spec.resources = "BBCustomLib/Classes/BBCustomLib.framework/BBCustomLib.bundle"
  # 依赖的系统库(framework)。多个可以使用英文逗号隔开
  spec.frameworks = ['UIKit','Foundation', 'Security', 'CoreTelephony', 'CoreLocation']
  # 依赖的系统dylib库，依赖这种库时不需要写前面的"lib"，如依赖"libz.tbd"，只需要写上'z'就行了
  spec.library   = "z"

  spec.static_framework = true
  spec.user_target_xcconfig = {'OTHER_LDFLAGS' => '-ObjC'}

  valid_archs = ['armv7', 'x86_64', 'arm64']
  spec.xcconfig = {
    'VALID_ARCHS' =>  valid_archs.join(' '),
  }
  spec.pod_target_xcconfig = {
      'VALID_ARCHS' => 'x86_64 armv7 arm64'
  }
end
```

为了避免错误，我们可以进行以下命令校验：

``` shell
$ pod lib lint --verbose
```

如果运行之后存在错误，则根据提示的错误进行修正，如果只是进行警告，我们可以选择忽略，比如：

``` shell
$ pod lib lint --verbose --allow-warnings
```

当终端出现 **passed validation** 时，表示已经符合规范。


### 3、添加你的 Podspec 到你的 repo

在前面验证通过的基础上，我们可以将创建的依赖库到自己的 spec 了。

``` shell
$ pod repo push BBSpecs BBCustomLib.podspec
```

执行完，如果失败会有相对应的警告和错误提示，只要按照警告和错误的详细信息进行修改和完善即可。


### 4、测试私有 pod

首先通过 XCode 创建一个普通 Demo 工程，然后添加 Podfile 文件：

``` shell
$ pod init
```

创建后对文件进行修改

``` ruby
source 'https://github.com/CocoaPods/Specs.git'               # 官方仓库地址
source 'http://git.xxx/bach/BBSpecs'     # 私有仓库地址

target 'Demo' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!
  pod 'BBCustomLib'
end
```

修改完成后，执行以下命令进行 Pods 依赖库的安装。

``` shell
$ pod install
```

安装完成后，即可通过新生成的工程文件来打开工程。


### 发布稳定版本，tag 的使用

删除本地 tag

```shell
$ git tag -d v1.0.0
```

删除远程 tag

```shell
$ git push -d origin v1.0.0
```


Follow your heart.
