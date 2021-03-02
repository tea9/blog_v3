---
title: kali nethunter nexus 5
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
abbrlink: 2066460757
date: 2021-02-22 17:56:04
tags:
---
**手机信息**
设备：nexus 5 
android 6.0.1
需要根据自己的手机设备进行下载
可安装kalinethunter的手机型号
https://www.offensive-security.com/kali-linux-nethunter-download/
Nexus 4/5、Nexus 7、Nexus 10、Nexus 6 、Nexus 9 、OnePlus 


## 下载

下载kali nethunter
https://bt4g.org/search/nethunter-2020-3
选择下载
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/39/4.png)
nethunter-2020-3-hammerhead-marshmallow-kalifs-full-zip
nethunter-2020.3-hammerhead-marshmallow-kalifs-full.zip  1.20GB
nethunter-2020.3-hammerhead-marshmallow-kalifs-full.zip.txt.sha256sum  122B

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/39/3.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/39/2.png)

hammerhead-nougat-kalifs-full-zip 

```
magnet:?xt=urn:btih:76c3043d9c9b3434b44e8838676b6515bab06ef5&dn=nethunter-2020-3-hammerhead-marshmallow-kalifs-full-zip&tr=udp://tracker.opentrackr.org:1337/announce&tr=udp://tracker.internetwarriors.net:1337/announce&tr=udp://exodus.desync.com:6969/announce&tr=udp://tracker.cyberia.is:6969/announce&tr=udp://explodie.org:6969/announce&tr=http://opentracker.i2p.rocks:6969/announce&tr=udp://47.ip-51-68-199.eu:6969/announce&tr=http://tracker1.itzmx.com:8080/announce&tr=http://open.acgnxtracker.com:80/announce&tr=udp://tracker.tiny-vps.com:6969/announce&tr=udp://open.stealth.si:80/announce&tr=udp://www.torrent.eu.org:451/announce&tr=udp://tracker.torrent.eu.org:451/announce&tr=udp://tracker.ds.is:6969/announce&tr=udp://retracker.lanta-net.ru:2710/announce&tr=udp://tracker.dler.org:6969/announce&tr=http://tracker4.itzmx.com:2710/announce&tr=udp://tracker.moeking.me:6969/announce&tr=udp://ipv4.tracker.harry.lu:80/announce&tr=udp://valakas.rollo.dnsabr.com:2710/announce&tr=http://tracker.bt4g.com:2095/announce
```
这个方法我一直连接不上资源
我选择后面的方式进行下载

https://www.offensive-security.com/kali-linux-nethunter-download/

下载
Nexus 5 Marshmallow (ZIP)	Torrent	2020.4	1.7G	555fe3aceb2b68a451b79edf4cd89f899513f1019f5d14b753e9fa1e85e04a9f
使用torrent文件进行下载
nethunter-2020.4-hammerhead-marshmallow-kalifs-full.zip.torrent
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/39/1.png)

刷机：
adb reboot bootloader
解压hammerhead-mmb29k-factory-1943f0f5.tgz
cd hammerhead-mmb29k
./flash-all.sh

```
 adb push nethunter-2020.4-hammerhead-marshmallow-kalifs-full.zip /sdcard
```

进入nexus5 Recovery
按音量下键+电源键

twrp:
https://twrp.me/
Google Nexus5，twrp-3.0.2-0-hammerhead.img
https://dl.twrp.me/hammerhead/twrp-3.0.2-0-hammerhead.img.html
adb reboot bootloader
fastboot devices 判断设备是否连接
fastboot flash recovery twrp-3.0.2-0-hammerhead.img
fastboot reboot
进入recovery安装kali nethunter
adb reboot recovery

刷入kali nethunter重启后就可以看到酷炫的kali界面就说明成功了
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/39/5.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/39/6.png)
现在看内核版本已经变了

https://images.kali.org/nethunter/nethunter-2019.3-hammerhead-marshmallow-kalifs-full.zip
nethunter-hammerhead-marshmallow-kalifs-full-2019.3.zip



没有成功 进度  测试了百度网盘nethunter-hammerhead-marshmallow-3.0.zip卡在界面上参考[为安卓手机刷上手机kali系统Nethunter](http://www.360doc.com/content/19/1023/23/56395733_868688367.shtml)
还没有尝试重刷系统测试 可以之后测试下


## LINKS
[ARM设备武器化指南·破·Kali.Nethunter.2020a.上手实操](https://www.anquanke.com/post/id/205455)  
[hammerhead-nethunter](https://7142857.com/2021/02/18/hammerhead-nethunter/#more)  
[为安卓手机刷上手机kali系统Nethunter](http://www.360doc.com/content/19/1023/23/56395733_868688367.shtml)
[Index of /nethunteros/CM14_1/hammerhead/](https://build.nethunter.com/nethunteros/CM14_1/hammerhead/)  