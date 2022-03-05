---
title: android objection的使用
abbrlink: 4055048667
date: 2021-03-29 10:52:31
description: android objection的使用
tags:
  - android安全
  - objection
categories:
  - - 安全
    - 移动安全
    - android安全
---

## 安装
> Objection是一款移动设备运行时漏洞利用工具，该工具由Frida驱动，可以帮助研究人员访问移动端应用程序，并在无需越狱或root操作的情况下对移动端应用程序的安全进行评估检查。
需要注意的是，该工具不涉及到越狱或root绕过，通过使用objection，我们可以很大程度地降低这些安全限制以及沙箱限制。
1、修复iOS和Android应用程序，嵌入了Frida实用工具。
2、与文件系统交互，枚举条目以及上传/下载的文件。
3、执行各种内存相关任务，例如列举加载的模块以及相关的输出。
4、尝试绕过或模拟越狱/root环境。
5、发现加载的类，并列举对应的方法。
6、执行常见SSL绑定绕过。
7、针对目标应用程序，从方法调用中动态导出参数。
8、与内联SQLite数据库交互，无需下载其他数据库或使用外部工具。
9、执行自定义Frida脚本。

功能：
同时支持iOS和Android。
检查容器文件系统并与之交互。
绕过SSL固定。
转储钥匙串。
执行与内存相关的任务，例如转储和修补。
探索和操纵堆上的对象。

[github地址](https://github.com/sensepost/objection)  

安装：
`pip3 install objection`
更新：
`pip3 install --upgrade objection`

## 使用

进入交互式分析环境：
`objection -g com.caratlover explore`
查看所有activity:
`android hooking list activities `
启动activity：
`android intent launch_activity com.chanson.business.MainActivity`


```
~ » objection -g com.caratlover explore                            
Using USB device `Google Pixel XL`


A newer version of objection is available!
You have v1.8.4 and v1.10.1 is ready for download.

Upgrade with: pip3 install objection --upgrade
For more information, please see: https://github.com/sensepost/objection/wiki/Updating

Agent injected and responds ok!

     _   _         _   _
 ___| |_|_|___ ___| |_|_|___ ___
| . | . | | -_|  _|  _| | . |   |
|___|___| |___|___|_| |_|___|_|_|
      |___|(object)inject(ion) v1.8.4

     Runtime Mobile Exploration
        by: @leonjza from @sensepost

[tab] for command suggestions
com.caratlover on (google: 8.1.0) [usb] #
```
