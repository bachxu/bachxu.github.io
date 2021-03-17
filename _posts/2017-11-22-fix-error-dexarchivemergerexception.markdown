---
layout: post
title:  "解决 Android 中出现 DexArchiveMergerException 异常"
date:   2017-11-22 15:32:54 +0800
categories: iTechnology
---
Android Studio 在程序编译中报 DexArchiveMergerException 异常，具体错误如下：

``` java
Error:Execution failed for task ':app:transformDexArchiveWithExternalLibsDexMergerForDebug'.
> java.lang.RuntimeException: java.lang.RuntimeException: com.android.builder.dexing.DexArchiveMergerException: Unable to merge dex
```

### 问题排查

源代码在 Windows 操作系统，Android Studio 2.0 环境下编译成功，但是通过 Git 进行 clone 到 macOS 操作系统， Android Studio 3.0 环境下编译出现 **DexArchiveMergerException** 异常。通过排查发现 [CityPicker](https://github.com/zaaach/CityPicker) 本身已经引入了高德地图定位 SDK，这与程序中又引入的高德地图定位 SDK 相冲突。

### 解决方法

在 app 的 build.gradle 文件中只添加高德地图 SDK，删除重复的高德提督定位 SDK。

``` gradle
dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:26.+'
    testCompile 'junit:junit:4.12'
    compile files('libs/AMap2DMap_5.2.0_AMapSearch_5.3.1_20170817.jar')
    compile 'com.zaaach:citypicker:1.1'
}
```
