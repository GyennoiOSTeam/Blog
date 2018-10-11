---
title: 开发中遇到的问题汇总
date: 2018-10-11 00:00:47
tags:
author: 彭凡
---

摘要:
 开发中遇到的问题汇总,包含解决方案,查漏补缺

<!-- more -->

### 目录
* [iOS12 无法获取WiFi的SSID](#chapter-1)
#### <span id="chapter-1">iOS12 无法获取WiFi的SSID</span>
简述:  
SSID全称Service Set IDentifier, 即Wifi网络的公开名称.在IOS 4.1以上版本提供了公开的方法来获取该信息.  
以下是获取SSID的代码:
```
+ (NSString *)wifiSSID {
     NSString *ssid = nil;
     NSArray *ifs = (__bridge_transfer id)CNCopySupportedInterfaces();
     for (NSString *ifnam in ifs) {
     NSDictionary *info = (__bridge_transfer id)CNCopyCurrentNetworkInfo((__bridge CFStringRef)ifnam);
     if (info[@"SSID"]) {
       ssid = info[@"SSID"];
  }
 }
     return ssid;
}
```
但是升级Xcode10之后,编译APP运行在iOS12系统的设备上,发现获取的SSID为nil.这里猜想可能是CNCopyCurrentNetworkInfo()方法在iOS12发生了改变.选中方法,查看文档,如下图:  

![iOS12 无法获取WiFi的SSID](https://raw.githubusercontent.com/pengfan123/images/master/ios12-WiFi%20Access.png)

解决方法: 按照如图所示,我们按照Target->capabilities->Access WiFi Information,把这个选项打开,然后登陆苹果开发者中心,为APP ID 添加 Access WiFi Information功能,然后更新provision file,重新下载安装provision file,编译项目运行,这时就可以获取WiFi的SSID了.
