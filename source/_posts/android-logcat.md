---
title: android adb logcat 根据包名打印日志
tags:
  - android安全
  - logcat
categories:
  - - 安全
    - 移动安全
    - android安全
abbrlink: 560848017
date: 2019-07-17 14:50:07
description: android adb logcat，android根据包名打印日志
---

android根据包名打印日志

```
Linux or Mac:
adb logcat | grep -F "`adb shell ps | grep com.abc.package | cut -c10-15`"
windows:
adb logcat | findstr com.abc.package

adb logcat -v time | find “XXX” >D:\myLog2.txt 
"XXX"可以用包名，或者关键字去代替。
```
