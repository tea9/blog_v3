---
title: pixel
abbrlink: 2142381090
date: 2020-11-22 08:17:04
tags:
  - android安全
  - pixel
categories:
  - - 安全
    - 移动安全
description: pixel刷机、magisk、frida、exposed配置
---

## 下载
下载对应镜像
https://developers.google.com/android/images#sailfish
下载twrp推荐3.0.0
https://dl.twrp.me/sailfish/twrp-3.3.0-0-sailfish.img.html
下载magisk 下载最新的
https://github.com/topjohnwu/Magisk/releases
frida-server
https://github.com/frida/frida/releases

## 安装
刷机
解压下载的`sailfish-opm4.171019.021.p1-factory-0bcf4315.zip`系统
adb reboot bootloader
执行 `./flash-all.sh`

### twrp
```
adb reboot bootloader
» fastboot devices                   tea@teadeMacBook-Pro
FA7530300871	fastboot

» fastboot flash recovery  twrp-3.3.0-0-sailfish.img 
Sending 'recovery' (31000 KB)                      OKAY [  0.941s]
Writing 'recovery'                                 (bootloader) Flashing active slot "_a" 
FAILED (remote: 'partition [recovery] doesn't exist')
fastboot: error: Command failed
如果有报错尝试执行
» fastboot boot twrp-3.3.0-0-sailfish.img
Sending 'boot.img' (31000 KB)                      OKAY [  0.773s]
Booting                                            OKAY [  0.906s]
Finished. Total time: 1.709s
```

### magisk
`adb push Magisk-v21.4.zip /sdcard`

安装-选择Magisk-v21.4.zip
安装成功选择reboot system

### frida
下载frida-server选择arm64
frida-server-14.2.13-android-arm64.xz
查看手机架构命令
`adb shell getprop ro.product.cpu.abi`
启动frida
```
 » adb shell                          tea@teadeMacBook-Pro
sailfish:/ $ su
sailfish:/ # cd /data/local/tmp
sailfish:/data/local/tmp # ls
fs1280arm64
sailfish:/data/local/tmp # ./fs1280arm64                                       
/system/bin/sh: ./fs1280arm64: can't execute: Permission denied
126|sailfish:/data/local/tmp # chmod 755 fs1280arm64  
sailfish:/data/local/tmp # ./fs1280arm64 
```
### exposed
下载magisk-riru
https://github.com/yangzhaofeng/riru-core
adb push magisk-riru-v21.1.zip /sdcard/Download 
magisk manager 
选择模块
从本地安装
选择magisk-riru-v21.1.zip
安装成功后重启

下载EdXposed-SandHook-v0.4.6.2.4529.-release.zip
https://github.com/ElderDrivers/EdXposed/releases/tag/v0.4.6.4
adb push EdXposed-SandHook-v0.4.6.2.4529.-release.zip /sdcard/Download
magisk manager 
选择模块
从本地安装
选择EdXposed-SandHook-v0.4.6.2.4529.-release.zip
安装成功后重启
重启后要很长时间要等好久 不要重启之类的操作哦

下载edxoosed manager
https://github.com/ElderDrivers/EdXposedManager/releases
adb install EdXposedManager-4.5.7-45700-org.meowcat.edxposed.manager-release.apk
