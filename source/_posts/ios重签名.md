---
title: ios重签名
abbrlink: 99406506
date: 2021-03-16 10:03:51
tags:
description: ios重签名
---

## 前言

我们遇到android app现在加固限制可能有很高的分析难度，不容易进行抓包，我们可以用ios的包脱壳然后进行分析会相对容易些，这篇文章主要讲了如何给ios设备越狱、给ios应用程序脱壳重签名，脱壳之后就可以进行分析代码、修改代码重签名之后可以在安装到设备上。

## 越狱

手机型号iphone6
手机版本：12.4.5
工具：爱思助手
因为我的版本是12.4.5所以选择CheckRa1n越狱
CheckRa1n越狱 重启后需要在越狱一遍
如果是其他版本查看爱思助手是否支持
用手机连接电脑-打开爱思助手选择刷机越狱-点击CheckRa1n越狱
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/1.png)
弹出Welcome to checkra1n！选择start进行刷机
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/2.png)
next
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/3.png)
选择start
1.点击start
2.按住side和home键看图片
3.松开side键但保持按住home键
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/4.jpg)
重启后刷机成功了
打开手机桌面上 checkra1n 选择按住cydia

### 安装frida

使用python3
我使用的是python3.7
安装frida
```
电脑端：
pip install frida
pip install frida-tools 
pip install objection  
```

手机端：
在cydia中添加frida源
https://build.frida.re
打开cydia-软件源-编辑-添加
点击添加源进行添加
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/5.png)
在cydia添加frida源后，搜索frida,根据iOS设备版本安装对应的frida服务端
选择安装 我选择的是frida for pre-A12，iPhone6s是A9处理器
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/6.png)

frida使用
```
查看frida版本号 正确显示版本号说明正确安装了
~ » frida --version                                           

14.2.13
查看设备
~ » frida-ls-devices                                               tea@teadeMBP
Id                                        Type    Name        
----------------------------------------  ------  ------------
local                                     local   Local System
4443c74f7b644116cdc90386281868199f45a13a  usb     iPhone      
socket                                    remote  Local Socket
--------
查看应用
 » frida-ps -U                                                    tea@teadeMBP
 PID  Name
----  --------------------------------------------------------
 925  Cydia
2495  ScreenshotServicesService
 236  checkra1n
2426  支付宝
2472  设置
 253  邮件
 151  AGXCompilerService
 126  AGXCompilerService
  74  AppleCredentialManagerDaemon
 170  AssetCacheLocatorS
查看运行的应用
~ » frida-ps -Ua                                                   tea@teadeMBP
 PID  Name       Identifier             
----  ---------  -----------------------
 925  Cydia      com.saurik.Cydia       
 236  checkra1n  kjc.loader             
2426  支付宝        com.alipay.iphoneclient
2472  设置         com.apple.Preferences  
 253  邮件         com.apple.mobilemail   
```
## 查看是否加壳
把ipa解压拿到二进制可执行文件
```
otool -l 可执行文件 | grep crypt

/xx.app» otool -l xx | grep crypt
     cryptoff 16384
    cryptsize 38797312
      cryptid 0

```
cryptid 0 = 无壳
cryptid 有壳为1，已脱壳为0）

## 砸壳
ios端app商店上线之前会由苹果商店进行FairPlayDRM数字版权加密保护简称加壳，如果我们要进行分析代码需要先进行脱壳

听大佬的推荐使用庆总的[frida-io-dump](https://github.com/AloneMonkey/frida-ios-dump)进行脱壳

```
下载frida-ios-dump
git clone https://github.com/AloneMonkey/frida-ios-dump.git 
cd frida-ios-dump                                                 
sudo pip install -r requirements.txt --upgrade
```

需要使用usbmuxd链接手机

下载usbmuxd
http://cgit.sukimashita.com/usbmuxd.git/snapshot/usbmuxd-1.0.8.tar.gz
用usb线连接手机
解压进入到python-client
```
cd python-client
执行即可将mac上的端口转发到ios上的端口 tcprelay.py -t iOS的端口:mac上的端口
python tcprelay.py -t 22:2222 
执行命令查看是否连接
ssh root@localhost -p 2222
默认密码alpine
```

```
执行脱壳脚本
可以写Name或者Identifier
cd frida-ios-dump 
python dump.py Aftenposten
脱壳的文件就在frida-ios-dump目录下
```
## 安装xcode
因为重签名需要下载xcode，如果是最新版的系统则可以在appstore上下载最新版本的xcode
如果是老版本需要在苹果官网上下载
https://developer.apple.com/download/more/?=xcode
选择对应版本的xcode进行下载
如何查询xcode版本号打卡appstore的xcode介绍页-选择版本历史记录
如我的电脑版本是macos Catalina 10.15.6可以下载xcode11.7版本
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/7.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/8.png)
下载成功后是个Xcode_11.7.xip文件
用系统自带的归档实用工具进行解压就得到xcode安装程序，在把程序拖到Applications在进行打开
1.保证存储空间 20G
2.去除解压验证
xattr -d com.apple.quarantine Xcode_11.7.xip
3.双击解压
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/9.png)

http://dantheman827.github.io/ios-app-signer/

## 重签名
github:https://github.com/DanTheMan827/ios-app-signer
http://dantheman827.github.io/ios-app-signer/
直接下载后解压安装就可以了
文档上有详细的使用步骤，主要注意的点是，要在xcode上创建项目，正常运行安装到手机
可能会遇到id重复，修改下id就可以了

对应真机的版本不一致在General-Deployment Info 中修改就可以
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/11.png)
id重复了在General-Identity修改id就可以
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/12.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/10.png)

Input File选择重签名的文件
Signing Certificate 选择对应的证书
Provisioning Profile 选择重签名描述文件(双击.mobileprovision文件即可。切勿选择 Re-Sign Only ，无效。)
点击Start开始
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/13.png)

[[Mac] 使用ios app signer 对app签名](https://www.52pojie.cn/thread-1083981-1-1.html)

## 查看签名信息
解压ipa文件
```
cd /Payload/xx.app
security cms -D -i embedded.mobileprovision
	<key>TeamIdentifier</key>
	<array>
		<string>RYPxxxH4N</string>
	</array>
	<key>TeamName</key>
	<string>Shxxxd</string>
	<key>TimeToLive</key>
	<integer>365</integer>
	<key>UUID</key>
	<string>b2dxxxb08</string>
	<key>Version</key>
	<integer>1</integer>
 最后面会输出相关信息
```
或者使用codesign
解压ipa文件
```
codesign -vv -d xx.app 
Executable=/Users/xx/Documents/Payload/xx.app/xx
Identifier=com.xx.xx
Format=app bundle with Mach-O thin (arm64)
CodeDirectory v=20400 size=412917 flags=0x0(none) hashes=12895+5 location=embedded
Signature size=4894
Authority=Apple Development: xx xx (UXxxxxx)
Authority=Apple Worlxxrity
Authority=Apple Root CA
Signed Time=Mar 16, 2021 at 5:33:54 PM
Info.plist entries=53
TeamIdentifier=RYxxxxxx
Sealed Resources version=2 rules=10 files=3414
Internal requirements count=1 size=176
```
确定签名信息跟之前ipa包不一致了就进行安装

## 安装IPA
安装可以使用手机助手pp助手或者爱思助手也可以使用xcode进行安装
打开xcode选择window-Devices and Simulators
选择加号进行安装
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/14.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/45/15.png)

## 查看日志
```
安装
brew install ideviceinstaller
idevicesyslog 
查看安装的日志
idevicesyslog | grep 'installd' --color  -A  10  -B  10  
```

## LINKS
[usbmuxd进行ssh连接-iOS逆向工程](https://blog.csdn.net/glt_code/article/details/65444592)
[ios逆向之frida安装与使用基础](https://www.jianshu.com/p/71587d8b39f4)
[低版本 macOS 如何正确安装 Xcode 合适版本，以 macOS 10.11.6 安装 Xcode 8.0作为演示](http://www.kungge.com/kwan/3350.html)
[Xcode打包ipa的基本步骤](https://www.jianshu.com/p/a19d2d0747ee)  