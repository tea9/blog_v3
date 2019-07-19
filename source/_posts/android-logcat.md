---
title: android adb logcat
tags:
  - android安全
abbrlink: 560848017
date: 2019-07-17 14:50:07
---

```
Linux or Mac:
adb logcat | grep -F "`adb shell ps | grep com.abc.package | cut -c10-15`"
windows:
adb logcat | findstr com.abc.package
```
