---
layout: post
title: "json&amp;xml"
date: 2015-09-24 16:44:36 +0800
comments: true
categories: Network
---
# 网络传输格式 JSON & XML

第一次接触这两个格式还是在学校做一个财务管理系统的时候。在之前变成都是纯粹的单机编程，不设计到网络数据传输的问题。到了这个项目才开始考虑如何在服务器和客户端之间传输数据。项目中使用的是JSON，所以在这里先讲JSON的部分。

##JSON

JSON是一种轻量化的数据格式。他的特点就是方便已用。目前90%的网络传输都是依赖于JSON格式。

首先来看看JSON格式是怎么样的。

```json

"access_token": "ACCESS_TOKEN",

   "expires_in": 1234,

   "remind_in":"798114",

   "uid":"12341234"
```

这是一个常见的JSON格式。比较容易的看出，其实就是一种key-value的形式。

key我们都好了解，是一个string。那么value就会有不一样的值。通过对比OC的对象，我们可以更容易的理解JSON中的数据类型。

				JSON					OC
				
				{}						NSDictionary
				
				[]						NSArray
				
				""						NSString
				
			   数字					  NSNumber

知道了JSON所对应的OC对象，我们就可以通过解析JSON对象，将相关的数据转换成OC对象来存储。在这里，介绍一种能够在本地模拟API的方式，这也是由于自己找不到很好的开发API所以想在本地能够模拟API的方式。


##JSON Server 模拟 API

###第一步 安装
* 安装Homebrew。这个可是个神器，能够管理mac上的第三方的库的工具。据说被作者写不出反转二叉树个谷歌拒了然后去了苹果。

`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" `

* 安装node.js

`brew install node `

* 安装json-server

`npm install -g json-server  
`

## 根据需求创建JSON

安装完毕后，随便找个你喜欢的文件夹，创建一个JSON格式的文件，如下
```JSON
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

然后保存。

###启动JSON-Server

通过命令`json-server --watch JSON文件的名字.json`来启动Server

到这里，我们就相当于在本地创建了一个服务器能够相应我们想对应的请求。

以上面的的JSON文件为例

![](/media/14430769630713.jpg)

当然，功能不止这么一点，JSON-Server还有更多方式，详见github[json-server](https://github.com/typicode/json-server)

##OC对象和JSON对象的转换

* 通过`NSJSONSerialization`来序列化JSON.

	
	![](/media/14430773793672.jpg)

输出的结果

![](/media/14430774045308.jpg)

对比上面的JSON数据，我们可以看出解析的结果是正确的

* 通过第三方库JSONKit来解析
 
 
 下载地址 [JSONKit](https://github.com/johnezang/JSONKit)
 


 ![](/media/14430745214364.jpg)



* 使用Mantle

在项目中用的最多的方法，也觉得非常好用

下载地址：[Mantle](https://github.com/Mantle/Mantle)

使用的方法：

1. 在本地创建对应的OC类
![](/media/14430784491980.jpg)


2. 实现相关的方法
		
![](/media/14430794069120.jpg)

第三种是直接将JSON通过一定的方法转成了OC的类对象。在使用的时候感觉更方便。



##XML

虽然JSON在网络传输中有90%的使用率，但是我们依然不能放弃10%的XML是吧。

XML全称是Extensible Markup Language，译作“可扩展标记语言”
跟JSON一样，也是常用的一种用于交互的数据格式
一般也叫XML文档（XML Document）

下面是一个典型的XML文件的结构

```
一个元素包括了开始标签和结束标签
拥有元素内容：<city>上海</city>
没有元素内容：<city></city>
没有元素内容的简写：<city/> 

一个元素可以嵌套若干个子元素（不能出现交叉嵌套）
<citys>
    <city>
        <name>上海</name>
        <weather>大暴雨</weather>
          <air>舒适</air>
    </city>
</citys>

规范的XML文档最多只有1个根元素，其他元素都是根元素的子孙元素
XML中的所有空格和换行，都会当做具体内容处理

一个元素可以拥有多个属性，属性值必须用 双引号"" 或者 单引号'' 括住。
<city name="上海" weather="大暴雨" air="舒适" />


属性表示的信息也可以用子元素来表示，比如

   <city>
        <weather>大暴雨</weather>
          <air>舒适</air>
    </city>
```

解析XML有两种方式

1. DOM解析：一次性将整个XML文档加载进内存，比较适合解析小文件
2. SAX解析：从根元素开始，按顺序一个元素一个元素往下解析，比较适合解析大文件

在iOS SDK里面，提供了两种解析的框架

1. NSXMLParser：它是基于objective-c语言的sax解析框架，是ios sdk默认的xml解析框架，不支持dom模式。
2. libxml2: 它是基于c语言的xml解析器，被苹果整合在ios sdk中，支持sax和dom模式。

同样，第三方也有许多解析XML的库。这里不打算讲第三方的，一个XML使用的比较少，一个使用SDK自身的效率往往已经非常好了。

###NSXMLParser

NSXMLParser属于SAX解析，是从上往下依次解析每个元素，在解析到每个元素的时候会通知代理，所以使用NSXMLParser必须遵守他的协议。
使用非常简单

![](/media/14430815879826.jpg)
从当前项目读取XML文件，读出数据，穿件解析器，设置代理，开始解析。

XML文件内容如下

![](/media/14430816475695.jpg)
设置代理后，我们要实现代理方法，主要有以下方法，直接上图了

![](/media/14430816906329.jpg)


![](/media/14430817012681.jpg)

![](/media/14430833160910.jpg)

![](/media/14430833236999.jpg)

###libxml2

重点在于导入libxml2的库

![](/media/14430836046841.jpg)


![](/media/14430836140287.jpg)


![](/media/14430836178120.jpg)


![](/media/14430836230051.jpg)


![](/media/14430836285068.jpg)

编译项目，通过了就没问题了。


用法参见[](https://github.com/neonichu/GDataXML)


##总结

在实际使用中，还JSON用的更多，所以对于JSON的解析过程要更加了解。对于XML，有一些公司任然会使用，而一些现存的数据也会用XML存储，所有也要有一定的了解。

这篇文章讲解了在网络通信中数据的格式。下一篇的话就进入iOS内如何进行网络通信了。虽然使用AFNetWorking已经有段时间了，但是对内部整个机制并不了解，要好好补课了。

