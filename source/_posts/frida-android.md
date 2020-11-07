---
title: frida_android
tags:
  - android安全
  - frida
categories:
  - - 安全
    - 移动安全
    - android安全
    - frida
description: frida配置
abbrlink: 1380470019
date: 2020-10-14 14:42:12
---

<!-- more -->

---


# frida_android

## 1 环境配置

### 1.1 安装Parallels Desktop虚拟机

windows可使用VMware虚拟机

[parallels-desktop](https://www.appstorrent.ru/61-parallels-desktop.html)  

滑到最下面有个绿的的下载按钮

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/01.png)

Parallels Desktop tools

https://jingyan.baidu.com/article/6b18230989d49dba59e15971.html

### 1.2 kali

[kali下载磁力](https://github.com/r0ysue/AndroidSecurityStudy/blob/master/FART/kali-linux-2019-4-vmware-amd64-zip.torrent)  

下载成功后解压缩后用Parallels Desktop打开Kali-Linux-2019.4-vmware-amd64.vmx文件，windows也可使用VMware打开.vmx文件

用户名/密码 root/toor

#### 1.2.1 修改时区

kali里面时间老是不对，其实只是时区不对而已，一个命令就搞定： # dpkg-reconfigure tzdata 然后选择Asia→Shanghai，然后重启即可。  

```
root@kali:~# dpkg-reconfigure tzdata

Current default time zone: 'Asia/Shanghai'
Local time is now:      Mon Jun  8 15:53:20 CST 2020.
Universal Time is now:  Mon Jun  8 07:53:20 UTC 2020.
```



#### 1.2.2 安装中文字体

KaliLinux默认不带中文，打开个中文网页，或者抓包时包里面有中文都看不了，其实只要安装个中文字体就可以，就可以看中文了。  

千万不要把系统改成中文的！~~会出很大问题~



如果还是没有中文就更新下apt-get

```
sudo apt-get update
sudo apt-get upgrade
apt install xfonts-intl-chinese
apt install ttf-wqy-microhei
```

#### 1.2.3 proxychains设置代理

我使用的v2rayU

https://github.com/yanue/V2rayU/releases/tag/2.3.1

获取mac电脑的ip ifconfig

开启sock端口监听 修改sock监听端口为局域网ip

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/02.png)






添加proxychains代理到kali

vim /etc/proxychains.conf

socks5  192.168.xx.xx 1080


![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/03.png)

proxychains curl ip.sb  测试连接

#### 1.2.4 htop

- htop工具 装个看`CPU`、内存、缓存的`htop`

apt install htop

使用：htop


![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/04.png)

#### 1.2.5 jnettop
网络监听  
安装方式一  

apt install jnettop

安装方式二  

 wget http://ftp.us.debian.org/debian/pool/main/j/jnettop/jnettop_0.13.0-1+b3_amd64.deb

dpkg -i jnettop_0.13.0-1+b3_amd64.deb



使用：jnettop

提示下面就要全屏幕使用

Could not read/find config file /root/.jnettop: No such file or directory.

Could not get HW address of interface any: No such device

Could not get HW address of interface bluetooth-monitor: No such device

Could not get HW address of interface nflog: No such device

Could not get HW address of interface nfqueue: No such device

Could not get HW address of interface dbus-system: No such device

Could not get HW address of interface dbus-session: No such device

Too small terminal (detected size: 75x25), minimum required size: 80x20

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/05.png)


#### 1.2.6 tmux

- 装个窗口分屏软件`tmux`

```
apt install tmux
```

http://www.ruanyifeng.com/blog/2019/10/tmux.html

进入 tmux

退出 exit

新建会话 tmux new -s <session-name>

查询会话 tmux ls

切换会话 tmux switch -t 0

杀死会话 tmux kill-session

```bash
# 划分上下两个窗格
$ tmux split-window

# 划分左右两个窗格
$ tmux split-window -h
```

```bash
# 光标切换到上方窗格
$ tmux select-pane -U

# 光标切换到下方窗格
$ tmux select-pane -D

# 光标切换到左边窗格
$ tmux select-pane -L

# 光标切换到右边窗格
$ tmux select-pane -R
```

```bash
# 当前窗格上移
$ tmux swap-pane -U

# 当前窗格下移
$ tmux swap-pane -D
```

#### 1.2.6 pyenv

安装依赖  

https://github.com/pyenv/pyenv/wiki/Common-build-problems

sudo apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev \

libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \

xz-utils tk-dev libffi-dev liblzma-dev python-openssl git

- 安装

https://github.com/pyenv/pyenv#installation

```
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc
重新打开个控制台
 exec "$SHELL"
pyenv version 查看版本

pyenv下载python
pyenv install 3.8.0
当前版本
pyenv version
全部版本
pyenv versions
切换版本
pyenv local 3.8.0
查看python版本
python -V
查看pip版本
pip -V
```

下载不成功需要连接代理哦

proxychains pyenv install 3.8.6



#### 1.2.7 vscode

搜索vscode保存文件后安装

~/Downloads# dpkg -i code_1.45.1-1589445302_amd64.deb 

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/06.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/07.png)


#### 1.2.8 node

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash

sudo apt update

sudo apt install -y nodejs
sudo apt install npm

```
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/08.png)


#### 1.2.9 安装adb

google platform tools

wget https://dl.google.com/android/repository/platform-tools-latest-linux.zip

修改名字

mv [platform-tools-latest-linux.zip](https://dl.google.com/android/repository/platform-tools-latest-linux.zip) xx.zip

解压

7z x [platform-tools-latest-linux.zip](https://dl.google.com/android/repository/platform-tools-latest-linux.zip)

添加环境变量

```
root@kali:~/Downloads/platform-tools# cat ~/.bashrc |grep export

export PYENV_ROOT="$HOME/.pyenv"

export PATH="$PYENV_ROOT/bin:$PATH"

root@kali:~/Downloads/platform-tools# pwd

/root/Downloads/platform-tools

root@kali:~/Downloads/platform-tools# export PATH="/root/Downloads/platform-tools:$PATH"



nano ~/.bashrc

添加

export PATH="/root/Downloads/platform-tools:$PATH"

推出nano

ctrl x - y

source ~/.bahrc
测试命令

adb
```

#### 1.2.10 jadx

https://github.com/skylot/jadx

proxychains  wget https://github.com/skylot/jadx/releases/download/v1.1.0/jadx-1.1.0.zip

mkdir jadx

unzip  jadx-1.1.0.zip -d /root/Downloads/jadx

cd jadx/bin

./jadx-gui

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/09.png)


#### 1.2.11 夜神模拟器

https://www.yeshen.com/

模拟器设置桥接模式

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/10.png)


####  1.2.12 Postern

https://github.com/postern-overwal/postern-stuff

下载

http://www.appchina.com/app/com.tunnelworkshop.postern

配置参考

https://mp.weixin.qq.com/s/ahPbBSfkkBsv4oy265rI2Q

#### 1.2.13 010 editor

https://www.sweetscape.com/010editor/

tar zxvf 010EditorLinux64Installer.tar.gz

#### 1.2.14 android studio

- android studio 开发IDE

下载

https://developer.android.com/studio/?gclid=CjwKCAjw8pH3BRAXEiwA1pvMsYJaveEEml02W7g7Xi-scuMLZl_dJ-5j7T_hgtdlF3ALYFRz4tN80BoCi30QAvD_BwE&gclsrc=aw.ds#downloads



wget https://redirector.gvt1.com/edgedl/android/studio/ide-zips/4.0.0.16/android-studio-ide-193.6514223-linux.tar.gz

tar zxvf [android-studio-ide-193.6514223-linux.tar.gz](https://redirector.gvt1.com/edgedl/android/studio/ide-zips/4.0.0.16/android-studio-ide-193.6514223-linux.tar.gz)

打开android stuido 一路默认 不设置代理 要等android stuido完全打开 要不然会有问题

```
/root/Desktop/android-studio/bin
./studio.sh 
```

platform-tools目录换成android stuido的

/root/Android/Sdk/platform-tools

设置环境变量

```
export PATH="/root/Android/Sdk/platform-tools:$PATH"
添加到~/.bashrc
nano ~/.bashrc
ctrl x
y
source ~/.bashrc
which adb
/root/Android/Sdk/platform-tools/adb
```



## 2 手机环境

#### 2.1下载镜像

大佬的刷机教程

https://github.com/r0ysue/AndroidSecurityStudy/blob/master/FRIDA/A01/README.md

官方工厂镜像

https://developers.google.com/android/images

下载pixel镜像8.1.0

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/14.png)

wget https://dl.google.com/dl/android/aosp/sailfish-opm1.171019.011-factory-56d15350.zip

解压系统

7z x sailfish-opm1.171019.011-factory-56d15350.zip

cd  sailfish-opm1.171019.011-factory-56d15350



下载后校验SHA-256 Checksum

```
openssl dgst -sha256 bullhead-opm7.181205.001-factory-5f189d84.zip
```



首先将手机进入`fastboot`状态，操作流程如下：

1. 将`USB`线断开，并确保手机有`80%`左右的电量；
2. 将手机完全关机；
3. 同时按住音量向下键和开机键；
4. 手机将进入`fastboot`状态；



连接手机

运行 ./flash-all.sh



#### 2.2安装twrp

建议使用twrp 3.3.0

https://dl.twrp.me/sailfish/twrp-3.3.0-0-sailfish.img.html

使用twrp3.3.0 不会乱码


![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/11.png)


复制粘贴到虚拟机上就可以

手机关机

音量键下和关机键同时按进入fastboot

fastboot boot twrp-3.3.1-0-sailfish.img

#### 2.3magisk

wget https://github.com/topjohnwu/Magisk/releases/download/v20.4/Magisk-v20.4.zip

adb push Magisk-v20.4.zip  /sdcard

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/12.png)


sailfish刷twrp遇到这种乱码文件夹的，先进Wipe里把Internal Storage 给wipe了，再reboot之后，fastboot boot twrp.img进入临时twrp就可以了。后续adb push都没有问题。



 第一遍刷入临时TWRP老是会出现/sdcard/下面显示类似加密的多个文件,进TWRP(Advanced Wipe/Format)后重新flash-all也没效果 # Wipe - Advanced Wipe - 选Internal Storage - Swipe to Wipe(一定要确认/sdcard/下面没有任何内容,有时候wipe后下面还有一个文件需要再wipe) - 后退到TWRP8选项界面 - Reboot - Bootloader- 重进bootloader - 再临时刷入TWRP

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/35/13.png)

https://dl.twrp.me/sailfish/twrp-3.3.0-0-sailfish.img.html

使用twrp3.3.0 不会乱码

## 3 frida环境

#### 3.1 frida-server 

```
adb shell getprop ro.product.cpu.abi #查看手机cpu
```

根据你的手机架构下载frida-server

推荐12.8.0

https://github.com/frida/frida/releases

这是我下载的版本

[frida-server-12.8.0-android-arm64.xz](https://github.com/frida/frida/releases/download/12.8.0/frida-server-12.8.0-android-arm64.xz)

```
解压frida-server
cd frida-server-12.8.0-android-arm64
root@kali:~/Desktop/frida-server-12.8.0-android-arm64# ls
frida-server-12.8.0-android-arm64
root@kali:~/Desktop/frida-server-12.8.0-android-arm64# mv frida-server-12.8.0-android-arm64 fs1280arm64
adb push  fs1280arm64 /data/local/tmp
adb shell 
su
cd /data/local/tmp
chmod 777 fs1280arm64
./fs1280arm64 -l 0.0.0.0:8888
```

#### 3.2 frida安装

实用FRIDA进阶：内存漫游、hook anywhere、抓包

https://www.anquanke.com/post/id/197657

```
pip install frida==12.8.0 
pip install frida-tools==5.3.0 
pip install objection==1.8.4
```

##### objection 插件 Wallbreaker

https://github.com/hluwa/Wallbreaker

git clone https://github.com/hluwa/Wallbreaker.git

```
 ./fs1280arm64

objection -N -g com.android.settings explore
plugin load /root/Desktop/Wallbreaker 
plugin wallbreaker classdump --fullname android.bluetooth.BuletoothDevice
plugin wallbreaker classdump --fullname sun.util.logging.PlatformLogger
```

##### objection+DEXDump脱壳

https://github.com/hluwa/FRIDA-DEXDump

git clone https://github.com/hluwa/FRIDA-DEXDump.git

```
 ./fs1280arm64

objection -N -g com.android.settings explore
plugin load /root/Desktop/FRIDA-DEXDump 
plugin dexdump search 
android intent launch_activity com.xx.xx.activity.MainActivity
plugin dexdump dump
新打开一个命令行窗口
cd root/com.xx.xx/
搜索MainActivity
grep -ril "MainActivity" *
```

#### 3.3 frida 开发环境

```
目前最新正确的Frida环境搭建方法： 
1. git clone https://github.com/oleavr/frida-agent-example.git
2. cd frida-agent-example/ 
3. npm install 
4. 使用VSCode等IDE打开此工程，在agent下编写typescript，会有智能提示。 
5. npm run watch会监控代码修改自动编译生成js文件 
6. frida -U -f com.example.android --no-pause -l _agent.js
```

#### 3.4 免ROOT使用frida

对于签名校验和文件校验就不行了

https://www.52pojie.cn/thread-1181471-2-1.html



#### 3.5 编写frida脚本

MainActivity.java  

```
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViewById(R.id.mBtnTest).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                helloAndroid();
                test1();
                test2(123);
                test3("str");
                test4("str",true);

                ((Button)findViewById(R.id.mBtnTest)).setText(test5());
            }
        });
    }

    private void helloAndroid() {
        System.out.println("helloAndroid");
    }

    private void test1() {
        System.out.println("test1()");
    }

    private void test2(int i) {
        System.out.println("test2(int)"+i);
    }

    private void test3(String s) {
        System.out.println("test3(String)"+s);
    }

    private void test4(String s,boolean b) {
        System.out.println("test4(String,boolean)"+s+","+b);
    }

    private String test5() {
        return "error";
    }
}
```

activity_main.xml  

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
    
    <Button
        android:id="@+id/mBtnTest"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</LinearLayout>
```

frida.js

```
console.log("[*] Starting script");
Java.perform(function () {
    var MainActivity = Java.use("com.demo.android_frida.MainActivity");
    MainActivity.helloAndroid.implementation = function () {
        console.log("helloAndroid()");
        // this.private_func();
    };
    MainActivity.test1.overload().implementation = function () {
        console.log("test1()");
        // this.private_func();
    };
    MainActivity.test2.overload("int").implementation = function (i) {
        console.log("test2(int): " + i);
        // this.private_func(i);
    };
    MainActivity.test3.overload("java.lang.String").implementation = function () {
        console.log("test3(String): " + arguments[0]);
        // this.private_func(arguments[0]);
    };
    //输出
    MainActivity.test4.overload("java.lang.String", "boolean").implementation = function (s, b) {
        console.log("test4(String,boolean): " + s + ", " + b);
        // this.private_func(s, b);
    };
    // 修改返回值
    MainActivity.test5.overload().implementation= function(){
        console.log("test5");
        // return this.test5()+"llll";
        return "ffff";
    }
});
```

注入

```
frida -U -l frida.js com.demo.android_frida

     ____
    / _  |   Frida 12.4.8 - A world-class dynamic instrumentation toolkit
   | (_| |
    > _  |   Commands:
   /_/ |_|       help      -> Displays the help system
   . . . .       object?   -> Display information about 'object'
   . . . .       exit/quit -> Exit
   . . . .
   . . . .   More info at http://www.frida.re/docs/home/
Attaching... 
```

点击按钮  

```
[*] Starting script
[LGE Nexus 5::com.demo.android_frida]-> helloAndroid()
test1()
test2(int): 123
test3(String): str
test4(String,boolean): str, true
test5
```

按钮文字改成ffff  