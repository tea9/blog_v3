---
title: HijackActivity android activity界面劫持
tags:
  - android安全
  - hijackactivity
categories:
  - - 安全
    - 移动安全
    - android安全
abbrlink: 2915342664
date: 2019-08-28 16:55:11
description: HijackActivity
---

activity劫持  
[HackAndroid](https://github.com/ZJsnowman/HackAndroid)  

我写了一个项目写的不太好
https://github.com/tea9/HijackActivity
使用：
修改HijackingService.java文件，mSadStories.put("com.xx.xx", AlipayStoryActivity.class);
修改com.xx.xx为你要劫持应用的包名。
需要在android4.4设备上使用生效。
先启动HijackActivity然后在启动劫持的app

界面劫持的原理：
开启一个服务定时去检索app，如果是被劫持的app就启动一个钓鱼界面，优化就是启动一个广播在开机启动时就启动HijackActivity进行劫持，更优化的是隐藏app的图标，这样就在手机启动就会进行劫持的一个服务，且用户几乎无感。

防范:
这是系统漏洞,目前的防护就是app切换了给用户进行提示提醒用户。建议app最低支持版本到android5.0.

隐藏app图标参考代码：
```
<activity android:name=".LaunchActivity">
     <intent-filter>
           <action android:name="android.intent.action.MAIN" />
           <data android:host="LaunchActivity" android:scheme="xx.xx.xx" tools:ignore="AppLinkUrlError"/>
           <category android:name="android.intent.category.LAUNCHER" />
     </intent-filter>
</activity>

```
在manifest的入口activity里面intent-filter中设置元素。
注：必须添加tools:ignore="AppLinkUrlError",否则会出错，host值为自定义，scheme值为包名

dada配置参考：http://www.cnblogs.com/shenhao/p/5947284.html

通过另一个APP隐式启动
```
Intent intent = new Intent();
ComponentName cn = new ComponentName("xx.xx.xx", "xx.xx.xx.LaunchActivity");
intent.setComponent(cn);
Uri uri = Uri.parse("xx.xx.xx.LaunchActivity");
intent.setData(uri);
startActivity(intent);
```

[Android之Activity界面劫持反劫持](https://blog.csdn.net/LVXIANGAN/article/details/79299005?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)
[安卓Activity劫持与反劫持](https://www.freebuf.com/company-information/222548.html)  
[Android-隐藏app图标以及隐式启动](https://blog.csdn.net/weixin_41182727/article/details/98934258)