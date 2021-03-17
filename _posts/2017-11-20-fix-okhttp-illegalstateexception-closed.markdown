---
layout: post
title:  "解决 okhttp 中出现的 java.lang.IllegalStateException:closed 问题"
date:   2017-11-20 16:16:20 +0800
categories: iTechnology
---
在 Android 开发中使用 okhttp 框架进行网络请求时出现 **java.lang.IllegalStateException:closed** 异常。

### 问题排查

通过问题检索，在 [okhttp](https://github.com/square/okhttp/issues/1240) 的 GitHub 开源库的相关问题中给出了相应解释，具体如下：

> Because response body can be huge so OkHttp doesn’t store it in memory, it reads it as a stream from network when you need it.
>
> When you read body as a string() OkHttp downloads response body and returns it to you without keeping reference to the string, it can’t be downloaded twice without new request.

由于响应正文可能很大，因此 okhttp 不会将其存储在内存中，因此在您需要时将其从网络中作为流读取。如果您没有使用引用来保存通过 okhttp 获取的响应正文并以 string() 函数返回的内容，除非存在新的请求，否则响应内容不能被第二次下载读取。

### 问题代码

``` java
@Override
public void onResponse(Call call, Response response) throws IOException {
    Log.i("HTTPINFO", response.body().string());
    String responseData = response.body().string();
    // 业务逻辑处理代码
    ...
}
```

### 解决方法

``` java
@Override
public void onResponse(Call call, Response response) throws IOException {
    String responseData = response.body().string();
    Log.i("HTTPINFO", responseData);
    // 业务逻辑处理代码
    ...
}
```
