---
layout: post
title: "让Xcode的控制台更给力点"
date: 2015-12-03 23:23:35 +0800
comments: true
categories: Xcode
---
用过Xcode的人都知道，Log是有多难堪多难堪，控制台各种蛋疼各种无语，看到APPCode的控制台后我时对xcode彻底无爱了。经过各种折腾，决定在当前的项目试验下增强log的功能，使用CocoaLumberjack和XcodeColors来使我们的控制台达到能看的效果。


首先 打开CocoaLumberjack的github地址

https://github.com/CocoaLumberjack/CocoaLumberjack



使用cocospod 安装

```
platform :ios, '8.0'
    pod 'CocoaLumberjack'
```    
    


在自己工程文件下.pch下加入

```
#define LOG_LEVEL_DEF ddLogLevel
#import <CocoaLumberjack/CocoaLumberjack.h>
```



在APPDelegate里面初始化log

```
- (void)initLogger
{
    
    // Standard lumberjack initialization
    [DDLog addLogger:[DDTTYLogger sharedInstance]];
    
    // And we also enable colors
    [[DDTTYLogger sharedInstance] setColorsEnabled:YES];
    
    [DDTTYLogger sharedInstance].logFormatter = [[DFCustomFormatter alloc] init];

    DDFileLogger *fileLogger = [[DDFileLogger alloc] init]; // File Logger
    fileLogger.rollingFrequency = 60 * 60 * 24; // 24 hour rolling
    fileLogger.logFileManager.maximumNumberOfLogFiles = 7;
    [DDLog addLogger:fileLogger];
    
    [self setConsoleColor];
    
 
}
```

到这里就完成了ddlog的初始化，但是到这里还没结束，因为这样仅仅是让框架运行起来，还没有到我们要的效果。

我们想要的，应该是达到以下的目的

1. 能够打印Log的发生位置，方法，时间
2. 能够根据不同Log级别有不同的颜色对应


完成第一点很简单，我们只需要实现自己的formatter就可以了


实现``DDLogFormatter``的协议

```
#import <Foundation/Foundation.h>
#import "DDLog.h"

@interface DFCustomFormatter : NSObject <DDLogFormatter>

```

```

@implementation DFCustomFormatter

- (NSString *)formatLogMessage:(DDLogMessage *)logMessage {
    NSString *logLevel;
    switch (logMessage->_flag) {
        case DDLogFlagError    : logLevel = @"E"; break;
        case DDLogFlagWarning  : logLevel = @"W"; break;
        case DDLogFlagInfo     : logLevel = @"I"; break;
        case DDLogFlagDebug    : logLevel = @"D"; break;
        default                : logLevel = @"V"; break;
    }
    
    NSString *formatStr = [NSString stringWithFormat:@"[%@ %@][line %lu] %@",
                            logMessage.fileName, logMessage->_function,
                           (unsigned long)logMessage->_line, logMessage->_message];
    return formatStr;

}

```

这样我们就打印了需要的信息，更多的方法可以到DDLogMessage里面看提供了什么属性。



完成第二点，就要借助XcodeColors

地址：https://github.com/robbiehanson/XcodeColors

安装后，在APPDelegate中初始化


```    
// 打开颜色支持
[[DDTTYLogger sharedInstance] setColorsEnabled:YES];
```

然后我们可以根据自己的喜好设置不同级别的Log的颜色

```
- (void)setConsoleColor
{
#if TARGET_OS_IPHONE
    UIColor *pink = [UIColor colorWithRed:(255/255.0) green:(58/255.0) blue:(159/255.0) alpha:1.0];
    
#else
    NSColor *pink = [NSColor colorWithCalibratedRed:(255/255.0) green:(58/255.0) blue:(159/255.0) alpha:1.0];
#endif
    
    [[DDTTYLogger sharedInstance] setForegroundColor:pink backgroundColor:nil forFlag:DDLogFlagInfo];
    [[DDTTYLogger sharedInstance] setForegroundColor:[UIColor redColor] backgroundColor:nil forFlag:DDLogFlagError];
    [[DDTTYLogger sharedInstance] setForegroundColor:[UIColor orangeColor] backgroundColor:nil forFlag:DDLogFlagWarning];

    
}

```

到这里，还不能让控制台显示出颜色，要设置对应schema

![](/media/14491562929176.jpg)



添加后，就可以让xcode的控制显示我们要的效果了。


如果自己的项目本身有对应的log方法，可以用宏直接替换








