---
layout: post
title: "warning是个好朋友"
date: 2015-09-14 22:40:16 +0800
comments: true
categories:other,iOS 
---

## 开头
 
这篇文章是在<http://www.cnblogs.com/dsxniubility/p/4757760.html>的基础上加入了一点自己的思考，主要内容都转自上文，所以算一篇文章的读后感+转载吧。
<!--more-->  
##正文
### iOS警告收录及科学快速的消除方法
警告，是在编码中出现的最多的问题。不同于error, warning的出现可能不并不影响程序的正常运行，所以很多时候许多程序员都会选择性的去忽略warning。但是作为一个优秀的程序员，我们应该立足于一个优秀的产品开发者，尽可能避免任何有可能使产品崩溃的现象出现。所以对一切warning都要做到尽可能的消除。

在iOS开发中，我也经常碰多许多摸不着头脑的warning。多数是因为版本的问题。由于苹果在不同版本之间会有一些区别，想要兼容多版本就要使用不同版本共有的方法。而一些stroyboard的问题更是没法定位，只能靠经验去分辨，在几个开发中已经发现了很多坑，而今天看到的这篇文章感觉非常不错，所以转载在这里给自己做一个笔记。


(下面为转载内容)


>详细科学的消除警告

> 其实笔者本意是想把一些第一眼看不懂比较坑的警告收录进来，但是后来发现那基本没几个需要些写了，所以就采用了全收录的方法，遇到的就记录下以后也会不断更新。 可以直接按command+F 在本页面搜索警告

> Unused variable 'replyURL'

> 1.没有使用

> Cannot find protocol definition for 'TencentSessionDelegate'

> 2.这种明明都能运行还说我没有定义的警告，是因为你这个协议虽然定义了，但是你这个协议可能还遵守了XX协议，然后这个XX协议没有定义导致会报这种警告，所以遇到这种警告要往“父协议”找。 举个栗子，上面这行就是腾讯授权的库里面报的警告，


>1 ``@protocol TencentSessionDelegate``
> 此协议遵守了TencentApiInterfaceDelegate协议，在TencentOAuth.h类中#import "TencentApiInterface.h" 警告可破

> Null passed to a callee that requires a non-null argument

> 3.这个警告比较新，是xcode6.3开始 为了让OC也能有swift的？和！的功能，你在声明一个属性的时候加上 __nullable（？可以为空）与__nonnull（！不能为空） 如果放在@property里面的话不用写下划线

> 1 ``@property (nonatomic, copy, nonnull) NSString * tickets;``
> 2  ``@property (nonatomic, copy) NSString * __nonnull tickets;``


> 或者用宏NS_ASSUME_NONNULL_BEGIN和NS_ASSUME_NONNULL_END 包住多个属性全部具备nonnull，然后仅对需要nullable的改下就行，有点类似于f-no-objc-arc那种先整体给个路线在单独改个别文件的思想。 此警告就是某属性说好的不能为空，你又在某地方写了XX = nil 所以冲突了。

> Auto property synthesis will not synthesize property 'privateCacheDirectory'; it will be implemented by its superclass, use @dynamic to acknowledge intention

> 4.他说你的父类实现了setget方法，但是如果你什么都不写，就会系统自动生成出最一般的setget方法，请用@dynamic 来承认父类实现的这个getset方法。

> Unsupported Configuration: Scene is unreachable due to lack of entry points and does not have an identifier for runtime access via -instantiateViewControllerWithIdentifier:.

> 5.一般是storyboard报的警告，简而言之就是你有的页面没有和箭头所指的控制器连起来，导致最终改页面可能无法显示。

> Deprecated: Push segues are deprecated in iOS 8.0 and later

> 6.iOS8之后呢，不要再用push拖线了，统一用show，他会自己根据你是否有导航栏来判断走push还是走modal

> Unsupported Configuration: Plain Style unsupported in a Navigation Item 

> 7.导航栏的item 不支持用plain ，那就用Bordered呗。

> The launch image set "LaunchImage" has 2 unassigned images.

> The app icon set "AppIcon" has 2 unassigned images.

> 8.几张图标还是启动图找不到自己的位置，可能是一次导入了全部尺寸图片，但是右边的设置只勾了iOS8的 那iOS7尺寸的图标就会报此警告。删掉，或者对照右边匹配。

> 'sizeWithFont:constrainedToSize:lineBreakMode:' is deprecated: first deprecated in iOS 7.0 - Use -boundingRectWithSize:options:attributes:context:

> 9.方法废除，旧的方法sizeWithFontToSize在iOS7后就废除了取而代之是boundingRectWithSize方法

> Undeclared selector 'historyAction'

> 10.使用未声明的方法，一般出现在@selector() 括号里写了个不存在的方法或方法名写错了。

> PerformSelector may cause a leak because its selector is unknown

> 11.这个和上面类似就是直接把上面那个@SEL拿来用会报这个警告

> 'strongify' macro redefined

> 12.这个宏声明重复,删一个吧

> 'UITextAttributeFont' is deprecated: first deprecated in iOS 7.0 - Use NSFontAttributeName

> 'UITextAttributeTextColor' is deprecated: first deprecated in iOS 7.0 - Use NSForegroundColorAttributeName

> 'UITextAttributeTextShadowColor' is deprecated: first deprecated in iOS 7.0 - Use NSShadowAttributeName with an NSShadow instance as the value

> 13.方法废除,一般一起出现

> Code will never be executed

> 14.他说这代码永远也轮不到他执行，估计是有几行代码写在了return之后

> Assigning to 'id' from incompatible type 'SXTabViewController *const __strong'

> 15.一般出现在xxx.delegate = self ，应该在上面遵守协议

> Format specifies type 'unsigned long' but the argument has type 'unsigned int' 

> 16.这个警告一般会出现在NSStringWithFormat里面 前面%d %lu 什么的和后面填进去的参数不匹配就报了警告

> Values of type 'NSInteger' should not be used as format arguments; add an explicit cast to 'long' instead

> 17.类似于上面，也是format里面前后写的不匹配

> Method 'dealWithURL:andTitle:andKeyword:' in protocol 'SXPostAdDelegate' not implemented

> 18.经典警告，遵守了协议，但是没有实现协议方法。 也可能你实现了只是又加了个参数或是你写的方法和协议方法名字有点轻微不同

> Using integer absolute value function 'abs' when argument is of floating point type

> 19.这个可以自动修正，就是说abs适用于整数绝对值，要是float取绝对值要用fabsf

> Attribute Unavailable: Automatic Preferred Max Layout Width is not available on iOS versions prior to 8.0

> 20.有的方法你用的太落后了，也有的方法你用的太超前了。 说这个最大宽度在iOS8之前的系统是要坑的

> Too many personality routines for compact unwind to encode

> 21.你可以在otherlink 中加入 -Wl,-no_compact_unwind 去掉该警告，根据苹果的解释，这个是由于某些地方 c/c++/oc/oc++混用会造成编译警告。一般没有什么伤害。

> Property 'ssid' requires method 'ssid' to be defined - use @synthesize, @dynamic or provide a method implementation in this class implementation

> 22.说这个ssid必须要定义个这个属性的getter方法，如果警告是setSsid就是setter方法， 用@synthesize和@dynamic 都行，一个是让编译器生成getter和setter，一个是自己生成，如果你有模型分发或kvc之类的，选@dynamic就行

> Unknown escape sequence '\)'

> 23.未知的转义序列。 一般有个斜杠再加个东西他都会以为是转义字符，一看\）不认识就报警告了，一般正则表达式容易报这种警告

> Property 'LoginPort' not found on object of type 'LoginLvsTestTask *'; did you mean to access property loginPort?

> 24.这种可以点击自动修复，是典型的大小写写错了，他提醒了一下。

> Variable 'type' is used uninitialized whenever switch default is taken

> 25.这是出现在switch语句中的警告， 一般可能是switch外面定义了个type但是并没有初始化（初始化操作都写在switch的各个分支里），然后在最后return type。 但是switch的有个分支没有对type初始化，他说如果你来到这个分支的话，那还没初始化就要被return。



##我们该怎么面对warning

从第一天开始编程，warning几乎比error都要更多的见到我。在正式做项目前，我几乎都是抱着“小婊砸哪里凉快去哪里的”态度的。在进入公司实习后，在某个强迫症的同事带领下，开始重视起来起来对warning的处理。事实上，warning应该比error更要重视。正是因为warning可以使编译通过，但是会造成我们并不知道的问题存在。这种“我知道你要挂但是我就是不告诉你”的事情，只有我们自己去解决warning，才是避免一切可怕的事情出现的根本。

有了强迫症，我们也可以通过warning为我们做点事情，比如通过``#warning TODO 来提示我们要需要注意的地方（特别是挖坑的时候）。

如果是自己写的文件或第三方库，有了新的接口，然后提示旧的接口废除的话需要在方法后加上宏NS_DEPRECATED_IOS和范围

``- (void)addTapAction:(SEL)tapAction target:(id)target NS_DEPRECATED_IOS(2_0, 4_0);``


如果需要在此方法后加上带信息的警告则需要这么写

``
- (void)addTapAction:(SEL)tapAction target:(id)target __attribute((deprecated("这个接口会爆内存 不建议使用")));
- ``

最后想说一句：重视warning，避免Bug，养成强迫症。


