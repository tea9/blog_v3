---
title: android AliCrackme分析
tags:
  - android安全
  - android ctf
categories:
  - - 安全
    - 移动安全
    - android安全
description: android AliCrackme分析、自毁程序密码.apk分析
abbrlink: 3513539175
date: 2021-03-26 14:01:23
---
参考链接：
https://www.52pojie.cn/thread-1315444-1-1.html
apk包：
链接: https://pan.baidu.com/s/1jXfS1qJyZDflKhgzZb8zMQ  密码: htop

aliCrackme一道经典ctf老题了，主要学习下，记录下解题过程
本题知识点：
so逆向分析、动态调试、反反调试
下载安装apk
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/1.png)
随便输入发现输出验证码校验失败
用jadx打开apk 搜索验证码校验失败
`jadx-gui 自毁程序密码_1.0原版.apk `
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/2.png)
分析代码，发现点击事件之后有个securityCheck方法，是native方法,需要分析so
`public native boolean securityCheck(String str);`
```
public void onClick(View v) {
                if (MainActivity.this.securityCheck(MainActivity.this.inputCode.getText().toString())) {
                    MainActivity.this.startActivity(new Intent(MainActivity.this, ResultActivity.class));
                    return;
                }
                Toast.makeText(MainActivity.this.getApplicationContext(), "验证码校验失败", 0).show();
            }
```
解压自毁程序密码_1.0原版.apk
用IDA打开"自毁程序密码_1.0原版/lib/armeabi/libcrackme.so"
搜索securityCheck方法，按F5看下伪c代、
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/3.png)

分析一下代码的执行，点击 v3 通过JNIEnv* 还原类似((_DWORD )v3 + 676))格式的指令，重命名以及注释的方式记录分析，如下图
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/4.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/5.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/6.png)

分析代码发现v6是真实代码，双击off_628C查看v6的值
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/7.png)
发现有个aWojiushidaan值，输入到密码框试试看
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/8.png)
发现仍验证码校验失败，需要动态调试查看真实的值
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/9.png)
IDA动态调试,要先吧android_server放到手机上
```
adb push /Applications/IDAPro7.0-tea/ida.app/Contents/MacOS/dbgsrv/android_server /data/local/tmp
adb shell
su
cd /data/local/tmp
chmod 777 android_server
./android_server
adb forward tcp:23946 tcp:23946
```
打开IDA，选择Debugger-Select debugger
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/10.png)
选择Remote ARM Linux/Android debugger
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/11.png)
选择Debugger-Process options
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/14.png)
填写Hostname、Port
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/15.png)
选择Debugger-Attach to process
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/12.png)
搜索com.yaotong.crackme，选择ok
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/16.png)
Ctrl+S 搜索crackme,选择带x的
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/17.png)
内存的绝对地址=so文件的基地址+要调试的函数的偏移量
记录基地址D7F1D6BC
在打开一个ida查找so文件偏移地址
mac 上 IDA双开
open -n /Applications/IDAPro7.0-tea/ida.app
打开后搜索选择check函数，按空格后查看地址，查看地址000011A8
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/18.png)
然后D7F1D6BC+000011A8=D7F1E864
经计算可知，函数的绝对地址就是D7F1E864在IDA中 使用快捷键G跳转到地址的位置，也就是要调试的函数位置
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/19.png)
按F2打断点，按F8继续运行（F8单步调试不进入函数F7单步调试进入函数
然后发现app直接崩溃了，应该是有反调试
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/27.png)

## 修改应用为debuggable

```
ro.debuggable app全局可调试
可读权限
mount -o remount,rw /
vi /system/etc/prop.default
修改为ro.debuggable=1
保存
mount -o remount,ro /
```
然后重启下手机
测试用debug模式启动app
adb shell am start -D -n com.yaotong.crackme/.MainActivity


## 查看进程
检测是否被调试：利用Linux系统 ptrace 来实现，当应用被调试时应用内存里的TracerPid字段就不为0，只要是不为0的时候，就会直接的退出程序，达到反调试的目的。

```
进入设备
adb shell
获取root权限
su
获取app的进程id
ps |grep 软件的包名
ps -e |grep com.yaotong.crackme
u0_a262      15716   648 1703812  74120 SyS_epoll_wait e9aa0504 S com.yaotong.crackme
查看进程的信息及TracerPid值： 
cat  /proc/进程ID/status
marlin:/ # cat /proc/15716/status
直接查看TracerPid
marlin:/ # cat /proc/15716/status |grep TracerPid                                                 
TracerPid:	0
```

用debug模式启动
adb shell am start -D -n com.yaotong.crackme/com.yaotong.crackme.MainActivity
IDA打开Debugger-Debugger options
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/13.png)
选择Suspend on thread start/exit、Suspend on library load/unload
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/20.png)

1.用调试模式打开app
 adb shell am start -D -n com.yaotong.crackme/com.yaotong.crackme.MainActivity
2.ida进行调试，调试选项勾选选择Suspend on thread start/exit、Suspend on library load/unload
**打开ida不用打开so直接进行调试**
3.启动jdb
 ps -e | grep com.yaotong.crackme
 adb forward tcp:8700 jdwp:4495（4495为上面获得的APP进程ID）
 jdb -connect com.sun.jdi.SocketAttach:hostname=127.0.0.1,port=8700
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/21.png)

```
 jdb -connect com.sun.jdi.SocketAttach:hostname=127.0.0.1,port=8700

设置未捕获的java.lang.Throwable
设置延迟的未捕获的java.lang.Throwable
正在初始化jdb...
> 
```
3.搜索crackme.so，在jni_onload上下断点
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/22.png)
app会停到没有加载的地方
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/23.png)
按F8然后Ctrl+s搜索crackme，如果没有就在进行单步调试直到搜索到crackme
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/24.png)
记录D7DB06BC然后静态找JNI_ONLoad的地址00001B9C
D7DB06BC+00001B9C=D7DB2258
按g跳转到该地址下断点，单步执行到jni_onload
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/26.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/25.png)
停到JNI_Onload里了
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/29.png)
BLX R7的位置跳了出去，很可疑的位置需要重点注意
静态分析找到R7的位置进行修改
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/30.png)
切换hex view
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/31.png)
用010打开crackme.so
找到37 FF 2F E1位置修改为 00 00 00 00
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/32.png)
然后保存
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/33.png)
反编译重打包app
```
apktool d 自毁程序密码_1.0原版.apk -o 1
替换so文件
打开1/dist目录给打包的app签名
java -jar uber-apk-signer-1.1.0.jar -a 自毁程序密码_1.0原版.apk --allowResign 
adb uninstall com.yaotong.crackme 
adb install 自毁程序密码_1.0原版-aligned-debugSigned.apk
```
正常启动app
打开IDA进行调试，给check函数下断点,在断点停下了
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/34.png)
按F5，查看v6的值发现是aiyou,bucuoo
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/35.png)
在输入框中输入
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/41/36.png)

## LINK
[[超级详细]实战分析一个Crackme的过程](https://www.52pojie.cn/thread-1315444-1-1.html)
[十六进制加法计算器](http://www.ab126.com/system/2783.html)  