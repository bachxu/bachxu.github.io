---
layout: post
title:  "iOS Framework 随记"
date:   2019-09-04 14:36:18 +0800
categories: iTechnology
---
iOS 开发者对 Framework 一定不会陌生，比如系统的 UIKit，Foundation等，比如第三方的 AppCenter。使用 Framework 可以更好地帮助我们去实现程序。

### 创建 Framework

因为 Framework 需要嵌套在 App 程序中才能发挥其价值，所以目前单独的 Framework 不能运行在模拟器或设备上。因此通常创建的 Framework 工程是嵌套在一个 Demo App 程序中，这样方便开发者进行调试以及进行可视化输出。

按照常规操作，创建一个 Demo App 工程。创建 Demo 完成后，在 XCode 菜单栏进行以下操作 【File】-> 【New】->【Project...】，在 Project 弹窗中找到并选择【Cocoa Touch Framework】。

在创建 Framework 过程中，须将 Framework 工程添加到 Demo App 工程中。Framework 创建成功后便可以进行编写相应的业务代码，实现其功能。

### 注意事项

- 只公开需要对外的头文件，其余的都不对外开放。通过 Framework 工程的【Build Phases】下的【Headers】，将需要公开的文件拖动到 `Public` 栏目中。
- 如果在静态库中使用了 Category，可能会碰到链接问题，需要在生成静态库的工程以及使用静态库的工程中使用 `all_load` 编译选项，即在对应 Target 的【Build Settings】下的【Other Linker Flags】选项添加 `-all_laod (建议先添加 -ObjC，如果依然有问题，再使用 -all_load)`。
- 生成静态库工程的【Build Settings】->【Architectures】->【Build Active Architecture Only】->【Release】选择 NO，Yes 表示只编译选中模拟器设备对应的架构，NO 表示为编译所有模拟器设备支持的架构。对外需提供支持所有架构的静态库。

对外提供的静态库须合并 Debug 以及 Release 模式下分别生成的编译文件，如果是纯手动的合并，每次操作都相对比较繁琐。我们可以通过在生成静态库的工程下新建一个【Target】，在【Target】弹窗中选择【Aggregate】。创建【Aggregate】完成后，在其【Build Phases】下的【Run Script】栏目添加以下内容。

``` shell
# Type a script or drag a script file from your workspace to insert its path.
##### 制作通用动态库(framework)

```ObjC
``` # Sets the target folders and the final framework product.

# 如果工程名称和Framework的Target名称不一样的话，要自定义FMKNAME

# 例如: FMK_NAME = "MyFramework"

FMK_NAME=${PROJECT_NAME}

# Install dir will be the final output to the framework.

# The following line create it in the root folder of the current project.

INSTALL_DIR=${SRCROOT}/Products/${FMK_NAME}.framework

# Working dir will be deleted after the framework creation.

WRK_DIR=build

DEVICE_DIR=${WRK_DIR}/Release-iphoneos/${FMK_NAME}.framework

SIMULATOR_DIR=${WRK_DIR}/Release-iphonesimulator/${FMK_NAME}.framework

# -configuration ${CONFIGURATION}

# Clean and Building both architectures.

xcodebuild -configuration "Release" -target "${FMK_NAME}" -sdk iphoneos clean build

xcodebuild -configuration "Release" -target "${FMK_NAME}" -sdk iphonesimulator clean build

# Cleaning the oldest.

if [ -d "${INSTALL_DIR}" ]

then

rm -rf "${INSTALL_DIR}"

fi

mkdir -p "${INSTALL_DIR}"

cp -R "${DEVICE_DIR}/" "${INSTALL_DIR}/"

# Uses the Lipo Tool to merge both binary files (i386 + armv6/armv7) into one Universal final product.

lipo -create "${DEVICE_DIR}/${FMK_NAME}" "${SIMULATOR_DIR}/${FMK_NAME}" -output "${INSTALL_DIR}/${FMK_NAME}"

rm -r "${WRK_DIR}"

open "${INSTALL_DIR}"
```

这样每次需要生成静态库时，只要选择【Aggregate】目标并进行编译即可自动合并两种环境的编译文件。

### 问题处理

#### Bitcode 问题

当 App 工程以及 Framework 都支持 Bitcode 时，发现在进行 Archive 操作时报错，报错内容列举如下：

``` shell
ld: bitcode bundle could not be generated because '/Users/liuyuning/Desktop/TestDecode/Decode/libDecode.a(Decode.o)' was built without full bitcode. All object files and libraries for bitcode must be generated from Xcode Archive or Install build for architecture armv7

clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

解决上述问题，需要在【Apple LLVM 7.0 - Custom Compiler Flags】中的【Other C Flags】以及【Other C++ Flags】中添加 `-fembed-bitcode` 选项。

#### 使用静态库工程出现 "Warning" No such file or directory 问题

解决方法是在创建静态库工程下的【Enable Clang Module Debugging】的值设置为 NO，重新编译后，使用静态库工程工程即不会出现上述警告。

另外有需要对生成的静态库大小进行控制，可以考虑在创建静态库工程的【Generate Debug Symbols】的值设置为 NO，这样可以除去当前工程的符号。

#### 如何查看当前 Framework 支持的框架

``` shell
$ lipo -info xxxx.framwork/xxxx
```

Follow your heart.

### 参考资料

- [Xcode7创建静态库和Framework](https://legacy.gitbook.com/book/xianjun/xcode7_framework_and_static_lib/details)
- [Bitcode在链接静态库时的问题处理](https://www.cnblogs.com/gejings/p/5541410.html)
- [iOS "Warning" No such file or directory详解、解决方案和原理](https://blog.csdn.net/Future_One/article/details/67640955)
- [减小静态库体积](https://www.jianshu.com/p/4415e7ee6f54)
