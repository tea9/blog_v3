---
title: android app脱壳
tags:
  - android安全
  - android加固
categories:
  - - 安全
    - 移动安全
    - android安全
    - android加固
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
abbrlink: 1498103013
date: 2021-03-21 08:56:47
description: android app脱壳
---

# android app脱壳、实战ctf题

## app加固威胁

- 代码修改(广告植入、替换广告id)
- 资源修改(界面替换广告，链接替换)
- 破解(应用收费，内购)
- 篡改数据(无限金币,钻石)
- 加入恶意代码(木马，隐私，交易)
- 动态注入，数据拦截，窃取，修改；协议修改

## app加固的目的

- 保护核心代码，防止被逆向，泄密 防止逆向分析-防止核心代码被反编译
- 防止营销作弊
- 防止代码被修改
- 控制被二次打包-校验完整性，签名，防止盗版
- 防止调试和注入-防止动态调试，注入获取关键数据
- 防止应用数据窃取-加密敏感数据
- 防止协议直接被盗刷-加密协议通信

## 常见加固厂商

- 加固保360
[360加固保](http://jiagu.360.cn/#/global/index)

- 娜迦nagapt
[娜迦加固](http://www.nagain.com/)  

- 梆梆安全bangcle
[梆梆加固](https://www.bangcle.com/)

- 爱加密ijm
[爱加密加固](http://www.ijiami.cn/appprotect_encrypt_way)

- [腾讯乐固](http://legu.qcloud.com/)

- [顶象安全](https://www.dingxiang-inc.com/business/stee)
- [几维安全](https://www.kiwisec.com/)
- [百度](https://apkprotect.baidu.com/?from=fccy2)
- 阿里 已经下线
- 盛大，网秦，通付盾

## 常见加固方式

类加载技术：  
针对apk中的classes.dex文件进行处理，放入待定的文件中，通过native代码来进行对其运行时解密。  

使用厂商：娜迦，爱加密，梆梆等。  

方法替换技术：  
将classes.dex中的方法中代码进行提取，抽取方法，在运行时对其动态进行解密还原  

梆梆，娜迦 

## 常用加固厂商特征

- 娜迦 libchaosvmp.so,libddog.so libfdog.so
- 爱加密 libexec.so,libexecmain.so
- 梆梆 libsecexe.so libsecmain.so libDexHelper.so
- 360 qihoo.util libprotectClass.so libjiagu.so
- 通付盾 libegis.so
- 网秦 libnqshield.so
- 百度 libbaiduprotect.so

1.用加固厂商特征：  

娜迦： libchaosvmp.so , libddog.solibfdog.so爱加密：libexec.so, libexecmain.so梆梆： libsecexe.so, libsecmain.so , libDexHelper.so360：libprotectClass.so, libjiagu.so通付盾：libegis.so网秦：libnqshield.so百度：libbaiduprotect.so 

## 加固技术的发展历史
第一代壳 Dex加密  
Dex字符串加密资源加密对抗反编译反调试自定义DexClassLoader  

第二代壳 Dex抽取与So加固  
对抗第一代壳常见的脱壳法Dex Method代码抽取到外部（通常企业版）Dex动态加载So加密  

第三代壳 Dex动态解密与So混淆  
Dex Method代码动态解密So代码膨胀混淆对抗之前出现的所有脱壳法  

第四代壳 arm vmp

- 第一代 DEX加密、字符串加密、资源加密、反调试、自定义DexClassLoader 
- 第二代 核心封装到So库/方法抽取/反调试
- 第三代 ELF变形/Ollvm混淆/多进程保护
- 第四代 代码虚拟化保护 DEX虚拟化保护
- 第五代 ARM虚拟化保护

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/1.jpeg)

## 脱壳的手法

- 修改系统源码自动脱壳
- 通过hook方式对关键函数进行脱壳
- 开源工具入ZjDroid，DexHunter进行脱壳
- 利用IDA或者GDB动态调试进行脱壳

## 反调试

反调试代码，都是在壳子的so中执行的，那么，我们只要在进入壳子第一行代码时下了断点，在调试过程中，找到了反调试检测代码的位置，直接干掉，直接绕过反调试  

在libdvm.so中方法loadNativeCode处下断点，android系统加载加载native代码也就是so文件都要走的函数，所以下在这里的断点，能够有效的找到加载壳子的so位置，能够在一进入壳子的so，直接断住。  

最简单的技巧，就是单步调试，F8一路按下去，按的多了，就有了更好的位置下断点。  

记住一点就好，壳子的so中，总有一处是解密还原出app原本的dex的。 

## 脱壳题目实战
链接: https://pan.baidu.com/s/1CELe8HRpGVmjg3NcuXpQGw  密码: 51o7

### 第一题 java_crackme.apk
java_crackme.apk
首先安装apk`adb install java_crackme.apk`
随便输入个值发现显示sorry，try again？
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/2.png)
再用jadx打开
发现使用了加固找不到入口类
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/1.png)
尝试脱壳使用葫芦娃大佬的[FRIDA-DEXDump](https://github.com/hluwa/FRIDA-DEXDump)
手机运行frida
[frida](https://github.com/frida/frida/releases)
```
adb shell
su
cd /data/local/tmp
./frida_server
```
打开app执行`python main.py`
要使用python3
```
~/Documents/tools/android/FRIDA-DEXDump(master*) » python main.py 
03-22/09:43:29 INFO [DEXDump]: found target [21626] com.kanxue.craceme
[DEXDump]: DexSize=0xbdb2c, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.kanxue.craceme/0x7e8dc04000.dex
[DEXDump]: DexSize=0x214c38, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.kanxue.craceme/0x7e8dcc2000.dex
[DEXDump]: DexSize=0x11c, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.kanxue.craceme/0x7e8e1ca4f5.dex
[DEXDump]: DexSize=0xbdb2c, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.kanxue.craceme/0x7e8e20d780.dex
[DEXDump]: DexSize=0xbdb2c, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.kanxue.craceme/0x7e8e6c301c.dex
[DEXDump]: DexSize=0x214c38, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.kanxue.craceme/0x7ea4a3b5c0.dex
```
已经脱下壳了
我们可以直接搜索sorry

```
cd com.kanxue.craceme
 grep -ril "sorry" ./*.dex
./0x7e8dcc2000.dex
```
用jadx打开这个dex
发现MainActive里之间写了flag然后输入到输入框里测试成功
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/3.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/4.png)

### 第二题 so_crackme_1.apk
安装后打开jni_enc
随便输入发现弹框`sorry，try again？`
用jadx打开apk发现加壳了
先进行脱壳

```
打开app 然后执行
python main.py 
03-24/10:54:56 INFO [DEXDump]: found target [16242] com.example.jni_enc
[DEXDump]: DexSize=0xc1da0, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.example.jni_enc/0x7e90326000.dex
[DEXDump]: DexSize=0x21d21c, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.example.jni_enc/0x7e903e8000.dex
[DEXDump]: DexSize=0xc1da0, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.example.jni_enc/0x7e9080d040.dex
[DEXDump]: DexSize=0x11c, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.example.jni_enc/0x7e90b994f5.dex
[DEXDump]: DexSize=0xc1da0, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.example.jni_enc/0x7e90d9e01c.dex
[DEXDump]: DexSize=0x21d21c, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.example.jni_enc/0x7ea903b5c0.dex
[DEXDump]: DexSize=0x4, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.example.jni_enc/0x7f2ca7a000.dex
[DEXDump]: DexSize=0x4, SavePath=/Users/tea/Documents/tools/android/FRIDA-DEXDump/com.example.jni_enc/0x7f2cb6e000.dex

cd com.example.jni_enc
grep -ril "sorry" ./*.dex
./0x7e903e8000.dex 
```
用jadx打开`0x7e903e8000.dex`
发现有jni函数，解压apk包用IDA打开`so_crackme_1/lib/armeabi-v7a/libnative-lib.so`文件

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/5.png)
看JNI_OnLoad好像没有混淆
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/6.png)
打开字符串窗口`View-Open subviews-Strings`搜索flag看看能不能搜索到
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/7.png)
输入试试
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/8.png)
### 第三题 so_crackme.apk
没做出来 参考菜菜文章
脱壳要先删除`rm -rf com.example.jni_enc`文件夹 包名跟第二题重复了
脱壳后发现flag还是写在so里面，用IDA打开`libnative-lib.so`
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/9.png)
看这么复杂应该是混淆了
用IDA进行调试

```
把android_server放入手机
/Applications/IDAPro7.0-tea/dbgsrv
adb push android_server /data/local/tmp
运行android_server
adb shell 
marlin:/ $ su
marlin:/ # cd /data/local/tmp 
marlin:/data/local/tmp # chmod 755 android_server 
启动android_server 默认端口为23946 指定端口启动 ./android_server -p12345
marlin:/data/local/tmp # ./android_server                                      
IDA Android 32-bit remote debug server(ST) v1.22. Hex-Rays (c) 2004-2017
Listening on 0.0.0.0:23946...
再打开一个控制台 转发调试端口
~ » adb forward tcp:23946 tcp:23946  
查看端口启动情况 win： netstat -a -n |findstr  23946
~ » netstat -a -n |grep  23946
tcp4       0      0  127.0.0.1.23946        *.*                    LISTEN 
```
打开IDA启动进程
IDA-Debugger-Select debugger-选择RemoteARM Linux/Android debugger
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/10.png)
在选择Debugger-Process options填写ip和端口 hostname 127.0.0.1 port 23946
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/12.png)
在选择Debugger-Attach tp process选择要调试的应用
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/43/13.png)

```
./android_server64 -p12345 
adb forward tcp:12345  tcp:12345 
```

debug模式启动
adb shell am start -D -n com.example.jni_enc/com.example.jni_enc.MainActivity

