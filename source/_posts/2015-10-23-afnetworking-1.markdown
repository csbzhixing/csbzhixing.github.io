---
layout: post
title: "AFNetworking 学习笔记 1"
date: 2015-10-23 10:31:15 +0800
comments: true
categories: Network 
---


##从3.0开始

一转眼，AF已经更新到了3.0版本。目前cocoapods上的最新版本是3.0 beta1。在3.0的版本里面，AF全面地使用`NSURLSession`代替了`NSURLConnection`。之前花了一些时间学习`NSURLSession`，在这里的学习终于派上了用场。在这里主要学习3.0版本的使用。希望在项目中能够顺利地过度到AFNetwoking 3.0版本。此外，随着Objective-c慢慢被Swift替代，AFNetworking 3.0可能是最后一个大版本更新。本文会一直随着AN的更新继续更新，也是一个不断学习的过程。



##结构

在3.0时代，AFN精简了结构，全面使用了`NSURLSession`。

![](/media/14451531667191.jpg)
beta1里面只剩下了当前几个Manager。

`AFHTTPSessionManager`是`AFURLSessionManager`的子类。

##AFURLSessionManager

`AFURLSessionManager`实现了以下几种`NSURLSession`的代理方法

#### `NSURLSessionDelegate`
 - `URLSession:didBecomeInvalidWithError:`
 - `URLSession:didReceiveChallenge:completionHandler:`
 - `URLSessionDidFinishEventsForBackgroundURLSession:`


#### `NSURLSessionTaskDelegate`

 - `URLSession:willPerformHTTPRedirection:newRequest:completionHandler:`
 - `URLSession:task:didReceiveChallenge:completionHandler:`
 - `URLSession:task:didSendBodyData:totalBytesSent:totalBytesExpectedToSend:`
 - `URLSession:task:didCompleteWithError:`

#### `NSURLSessionDataDelegate`

 - `URLSession:dataTask:didReceiveResponse:completionHandler:`
 - `URLSession:dataTask:didBecomeDownloadTask:`
 - `URLSession:dataTask:didReceiveData:`
 - `URLSession:dataTask:willCacheResponse:completionHandler:`

#### `NSURLSessionDownloadDelegate`

 - `URLSession:downloadTask:didFinishDownloadingToURL:`
 - `URLSession:downloadTask:didWriteData:totalBytesWritten:totalBytesWritten:totalBytesExpectedToWrite:`
 - `URLSession:downloadTask:didResumeAtOffset:expectedTotalBytes:`

###成员

####属性

在`AFURLSessionManager`中，主要的三个属性如下

![](/media/14451544109695.jpg)


`session`实现了会话，`operationQueue`是一个操作队列。`responseSerializer`是实现了`AFURLResponseSerialization`协议的一个对象。

***

Manager中还包括了安全协议的对象和连通性的对象。这两个类将在后面谈到。

![](/media/14451545558576.jpg)


***

下面是Task的内容，包含与当前`Session`中

![](/media/14451547233232.jpg)

***

回调块队列，包括了在主队列和私有队列的两个部分

![](/media/14451548418504.jpg)
 
####方法
 
 初始化方法
 
 ![](/media/14455211888103.jpg)

***

创建一个``NSURLSessionDataTask``数据性任务

![](/media/14455212385758.jpg)

***

创建``NSURLSessionUploadTask` 上传任务

![](/media/14455213126912.jpg)
![](/media/14455213190110.jpg)

***

创建``NSURLSessionDownloadTask`` 下载任务

![](/media/14455213738134.jpg)
![](/media/14455213826398.jpg)

***

获得一个特定任务的``progress进度``

![](/media/14455214651069.jpg)
![](/media/14455214692883.jpg)
![](/media/14455214742512.jpg)

***

``Session Delegate Callbacks 设置会话代理回调``

![](/media/14455217046547.jpg)
![](/media/14455217113671.jpg)

``Task Delegate Callbacks 设置任务代理回调``


当任务需要一个新的请求体发送给服务器的时候。
![](/media/14455219470133.jpg)

当HTTP请求回调有重定向的的话设置这个Block
![](/media/14455219533707.jpg)

当一个请求需要特别的鉴权的时候设置这个challenge
![](/media/14455219599178.jpg)

设置一个block去追踪上传进度

![](/media/14455658294340.jpg)

设置一个block当任务完成后执行
![](/media/14455658635389.jpg)


####``Setting Data Task Delegate Callbacks 设置数据任务代理的回调``

设置一个在数据任务获得response的时候回调block

![](/media/14455662011592.jpg)

设置一个block当数据任务变成下载的任务的时候执行

![](/media/14455662317310.jpg)

设置一个block当数据任务获得到数据的时候
![](/media/14455662434172.jpg)

设置一个block绝对是否缓存数据任务

![](/media/14455662958106.jpg)


####``Download Task Delegate Callbacks 下载任务代理回调``

设置block当下载任务完成下载后

![](/media/14455666105437.jpg)

设置block去追踪下载任务进度情况

![](/media/14455666301025.jpg)

设置block当下载任务执行/恢复的时候 执行

![](/media/14455666958363.jpg)


头文件的内容基本就是以上的了。可以看到整个AF的体系非常清晰完整，没有多余的东西，头文件只暴露了应该暴露的东西，值得我们去学习。



##使用的例子


###使用``AFURLSessionManager``

从源码中可以看到，``AFURLSessionManager``实现了

```Objective-c
NSURLSessionDelegate, NSURLSessionTaskDelegate, NSURLSessionDataDelegate, NSURLSessionDownloadDelegate, NSSecureCoding, NSCopying

```

首先需要设置url和NSURLConfirguration

这里是使用百度API商店的公开API
![](/media/14453928846040.jpg)

然后初始化Manager![](/media/14453929237373.jpg)

设置ResponseSerializer
![](/media/14453931412125.jpg)

初始化request
![](/media/14453932169242.jpg)


对request进行相关设置
![](/media/14453932396509.jpg)

根据request生成对应的``NSURLSessionTask``。
![](/media/14453935717679.jpg)



执行任务
![](/media/14453936154108.jpg)


来看看执行后的信息

![](/media/14453937344699.jpg)

这里由于使用的``AFHTTPResponseSerializer``(API的问题，仅仅支持text/plain)所以在获取的数据后，我们自己要json序列化。如果是设计好的API,直接使用``AFJSONRequestSerializer``就可以在回调中获取到json格式的数据了。

可以看到，整个使用还是很方便的。我们可以根据自己的需求配置不同设置。

