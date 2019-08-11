---
title: activity组件导出实验
tags:
  - android
  - android安全
abbrlink: 2970212528
date: 2019-08-09 18:20:11
---

## 前言 

原因在于导出activity，任何软件都可以调用它，包括攻击者编写的软件，可能产生恶意调用，应用会产生拒绝服务等问题。  

遇到这样的问题，如果它们只被同一个软件中的代码调用，将activity属性改为android:exported=”false”，如果组件需要对外暴露，应该通过自定义权限限制对它的调用。  

## 如何启动其他app的activity

1.通过applicationId和activity名启动  
启动方代码：  

```
ComponentName component = new ComponentName("com.demo.homeapp", "com.demo.homeapp.TestActivity");
Intent intent = new Intent();
intent.setComponent(component);
startActivity(intent);
```

被启动方代码：

```
<activity android:name=".TestActivity" android:exported="true"/>
```

2.通过自定义action启动  

启动方代码：  

```
Intent intent = new Intent();
intent.setAction("com.demo.homeapp.test");
startActivity(intent);
```

被启动方代码：  

```
<activity android:name=".TestActivity">
    <intent-filter>
        <action android:name="com.demo.homeapp.test"/>
        <category android:name="android.intent.category.DEFAULT"/>
    </intent-filter>
</activity>
```

3.通过packageManager获取拥有对应软件包名(ApplicationId)的App的Launch活动。

启动方代码：  
```
Intent intent = getPackageManager().getLaunchIntentForPackage("com.demo.homeapp");
startActivity(intent);
```
4.通过<data/>设置可以响应的指定数据类型。 

```
android:scheme。指定数据的协议部分。

android:host。指定数据的主机部分。

android：port。指定数据的端口部分。

android：path。指定主机名和端口后的部分，即相对路径。

android:mimeType。指定可以处理的数据类型，允许使用通配符的方式进行指定。

一个activity可以响应多个android:scheme，当一条<data/>中同时存在host、port、path时，
数据的格式需要满足该条<data/>中指定的所有协定。<data/>属性指定的响应数据类型，在网页中也能得到响应例如：

<a href="tea://...">

```

启动方代码:  


    Intent intent = new Intent();
    intent=new Intent("com.demo.homeapp.test", Uri.parse("tea://hello"));
    startActivity(intent);



被启动方代码:  

    <activity android:name=".TestActivity" >
        <intent-filter>
            <action android:name="com.demo.homeapp.test"/>
            <category android:name="android.intent.category.DEFAULT"/>
            <data android:scheme="tea"/>
        </intent-filter>
    </activity>




## 如何解决恶意启动带来的问题  

### 主动设置android:exported="false"

```
<activity android:name=".TestActivity" android:exported="false">
    <intent-filter>
        <action android:name="com.demo.homeapp.test"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:scheme="tea"/>
    </intent-filter>
</activity>
```

在启动会提示Permission Denial  
![](https://coding.net/u/tea9/p/image/git/raw/master/blog_img/32/1.png)  

### 设置权限
TODO



## activity导出-拒绝服务

如果intent传入空的，类型错误的等数据，导致activity报错，就存在拒绝服务漏洞

[activity拒绝服务](https://tea9.xyz/post/2470166639.html#%E6%8B%92%E7%BB%9D%E6%9C%8D%E5%8A%A1)  

## activity导出-泄露数据

如跟其他配置不当组合从而发生泄漏数据风险。  
webview file控制不当加上activity组件导出就可以导致敏感数据泄露。  
[webview file域控制不严格读取内部私有文件](https://tea9.xyz/post/3957115657.html)  

## LINKS
[Android Intent的隐示启动（启动其他APP界面并传递数据）](https://www.jianshu.com/p/821b76a713fe) 
[Android中通过其他APP启动Activity的四种方式](https://blog.csdn.net/weixin_36570478/article/details/81324698)  
[Android:跨应用启动Activity](https://blog.csdn.net/qq_40740256/article/details/83625403)  
