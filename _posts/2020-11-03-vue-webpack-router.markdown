---
layout: post
title:  "vue-router 报 Cannot find module XXX 问题"
date:   2020-11-03 16:00:45 +0800
categories: iTechnology
---
接手公司项目后，在运行后发现部分页面在点击导航栏后无法跳转，通过浏览器控制台输出发现报以下错误

`[vue-router] Failed to resolve async component default: Error: Cannot find module "@/views/xxx/xxx"`

`[vue-router] uncaught error during route navigation: ....`

### 解决方法

找到报错的路由方法代码位置，将

``` js
component: () => import('@/views/login/index'),
```

修改为

``` js
component: (resolve) => require(["@/views/login/index"], resolve),
```

如果是动态路由则

``` js
export const loadView = (view) => { // 路由懒加载
  return () => import(`@/views/${view}`)
}
```

改为

``` js
export const loadView = (view) => { // 路由懒加载
  return (resolve) => require([`@/views/${view}`], resolve)
}
```

错误原因是由于修改了 webpack 打包逻辑，webpack4 中动态 import 不支持变量方式，该修改对于生产环境无影响，只在开发环境有问题。

**环境信息：node 版本为 v14.15.0，npm 版本为 6.14.8**

Follow your heart.
