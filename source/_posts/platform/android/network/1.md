---
title: HttpClient 的 NoHttpResponseException 解决方案
tags: [网络]
---
最近公司在搞离线缓存，涉及视频的分片下载，也就是断点续传问题，下载过程中遇到了一个比较严重的问题，就是请求服务器的时候返回 NoHttpResponseException，导致下载率直线下降。搜索了国内和国外的文章，基本都没有解决问题，在这吐吐槽，索性自己尝试解决一下。


在解决这个问题的时候，我想说下我的断点续传解决方案,我用的是 HttpClient4.3.2，之所以选择HttpClient，主要是因为我们的软件要求支持2.2以上，所以2.3以下还是得用 HttpClient，因为官网是这么说的：

`Apache HTTP client has fewer bugs in Android 2.2 (Froyo) and earlier releases. For Android 2.3 (Gingerbread) and later,HttpURLConnection is the best choice.`

Android 上面用的都是比较旧的 HttpClient，bug也比较多，所以建议用最新稳定版的HttpClient，最新的 HttpClient 直接放到项目里是不可以的，所以我用了一个老外的 httpclientandroidlib-1.2.1.jar，这个人一直在维护更新，搜索引擎搜一下就行了，他封装的是4.3.2版的 HttpClient，这是个在 stackoverflow上面搜到的，新版的 HttpClient 解决了很多bug，这里就不多说了，直接去官网看吧。说一下NoHttpResponseException 问题,这个问题其实就是大意导致的首先看下面的代码：

```java
httpGet.addHeader("Range", "bytes=" + alreadySize + "-"+ totalSize);  
```

在这句之后执行请求，就会出现 NoHttpResponseException 。(根据服务器具体情况，如果兼容错误的服务器，不会报出异常，只说异常情况下的)

**分析**

这里执行：

```java
httpGet.addHeader("Range", "bytes=0-");  
```

返回的 Response 的 Header 中的部分信息如下：

```html
Content-Range: bytes 0-97103319/97103320
```

0 代表开始位置，97103319 表示服务器可以接受的最大位置(结束位置)，97103320 表示文件的长度。

问题出现了，我上面请求 range 的时候给的是文件长度 (97103320)，而服务器能接受的是 97103319，超出了范围，所以服务器返回了 NoHttpResponseException。

再看 HttpClient 源码, NoHttpResponseException 是从什么地方抛出来的：

```java
// clear the buffer  
            this.lineBuf.clear();  
            final int i = sessionBuffer.readLine(this.lineBuf);  
            if (i == -1 && count == 0) {  
                // The server just dropped connection on us  
                throw new NoHttpResponseException("The target server failed to respond");  
            }  
```

由上面的代码可知，readLine 的时候，i=-1，所以抛出了 NoHttpResponseException，为什么 i=-1呢，其实是因为我们 range 请求的时候，超出了范围，导致服务器未返回数据，致使 readLine 为-1。

**解决方案**

```java
httpGet.addHeader("Range", "bytes=" + alreadySize + "-"  
                    + (totalSize-1));  
```

这样请求之后，服务器就可以正常返回 response 了。

由此可以得出结论，range 超出范围可能只是其中的一种出错原因，还有可能其他配置参数或者请求信息不正确导致服务器不能正确返回 response，致使 HttpClient 抛出 NoHttpResponseException，所以建议出现这种错误的时候，检查一下header等位置，是否符合规定。

此文只是个人记录，参考为主，如果说的不正确，请评论指出，互相学习，抛砖引玉，谢谢！Peace&love