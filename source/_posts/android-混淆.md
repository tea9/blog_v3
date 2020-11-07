---
title: android_混淆
abbrlink: 135908078
date: 2019-08-28 17:03:37
tags:
  - android开发
  - android混淆
  - 代码混淆
categories:
  - - 开发
    - 移动开发
    - android开发
description: android混淆
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
---

minifyEnabled设置true
```
app/build.gradle
buildTypes {
    release {
        minifyEnabled true
        proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
    }
    debug{
        minifyEnabled true
        proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
    }

}
```

规则  
app/proguard-rules.pro  

## LINKS
[关于Android混淆的一些经验](https://www.jianshu.com/p/cac416194a12)  
