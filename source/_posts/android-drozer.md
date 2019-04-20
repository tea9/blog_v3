title: android drozer
abbrlink: 1688426988
date: 2019-04-11 13:38:47
category:
  - android安全
tags:
  - android安全
---
## 介绍
> dorzer是一个android开源安全测试工具

## 安装

[drozer官网](https://labs.mwrinfosecurity.com/tools/drozer)  
[drozer github](https://github.com/mwrlabs/drozer)  
[drozer.whl](https://github.com/mwrlabs/drozer/releases)  
客户端[Agent.apk](https://github.com/mwrlabs/drozer/releases/download/2.3.4/drozer-agent-2.3.4.apk)  
测试包[sieve.apk](https://github.com/mwrlabs/drozer/releases/download/2.3.4/sieve.apk)  
[官方使用文档](https://labs.mwrinfosecurity.com/assets/BlogFiles/mwri-drozer-user-guide-2015-03-23.pdf)  

**手机获得root权限**  

```
adb install drozer-agent-2.3.4.apk
adb install sieve.apk

1.Agent.apk 打开Embedded Server Enabled
2.adb forward tcp:31415 tcp:31415
3.drozer console connect

➜  Downloads drozer console connect         
Selecting 8f498d40e02d7b23 (Xiaomi MI 4W 6.0.1)

            ..                    ..:.
           ..o..                  .r..
            ..a..  . ....... .  ..nd
              ro..idsnemesisand..pr
              .otectorandroidsneme.
           .,sisandprotectorandroids+.
         ..nemesisandprotectorandroidsn:.
        .emesisandprotectorandroidsnemes..
      ..isandp,..,rotectorandro,..,idsnem.
      .isisandp..rotectorandroid..snemisis.
      ,andprotectorandroidsnemisisandprotec.
     .torandroidsnemesisandprotectorandroid.
     .snemisisandprotectorandroidsnemesisan:
     .dprotectorandroidsnemesisandprotector.

drozer Console (v2.4.4)
dz> 

```
## 使用

```
dz> run app.package.list -f sieve #关键字查找包名
com.mwr.example.sieve (Sieve)

dz> run app.package.info -a com.mwr.example.sieve #查看包信息
Package: com.mwr.example.sieve
  Application Label: Sieve
  Process Name: com.mwr.example.sieve
  Version: 1.0
  Data Directory: /data/user/0/com.mwr.example.sieve
  APK Path: /data/app/com.mwr.example.sieve-1/base.apk
  UID: 10152
  GID: [3003]
  Shared Libraries: null
  Shared User ID: null
  Uses Permissions:
  - android.permission.READ_EXTERNAL_STORAGE
  - android.permission.WRITE_EXTERNAL_STORAGE
  - android.permission.INTERNET
  Defines Permissions:
  - com.mwr.example.sieve.READ_KEYS
  - com.mwr.example.sieve.WRITE_KEYS

```


## LINKS

[Drozer 安装和使用 (Mac)](https://www.jianshu.com/p/168cdd3daa1d)  
[Drozer安装(Mac系统)和使用](https://www.jianshu.com/p/65fc2d52c7b1)  
