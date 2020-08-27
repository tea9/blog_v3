---
title: frida学习笔记
abbrlink: 1079078189
date: 2020-07-13 21:39:30
tags:
  - frida
  - android安全
category: 
  - frida
password: frida
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
description: rrr

---

# frida学习笔记

[frida](https://github.com/frida/frida)  

## frida环境搭建
使用r0ysue[kali](https://github.com/r0ysue/AndroidSecurityStudy/blob/master/FART/kali-linux-2019-4-vmware-amd64-zip.torrent)vm虚拟机环境，下载解压后可直接使用，用户名：root密码：toor

- 推荐使用proxychains设置代理  
win电脑使用的代理v2ray，在服务设置中查看socks5的端口号
打开v2ray允许来自局域网的链接
获取win电脑的ip ipconfig
添加proxychains代理
vim /etc/proxychains.conf
socks5  192.168.99.163 1080

- 使用pyenv管理python版本
[安装说明](https://github.com/pyenv/pyenv#installation)  

```
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc
在重新打开个控制台
exec "$SHELL"
查看环境变量
cat .bashrc
source ~/.bash_profile
安装python
pyenv install 3.8.0
查看版本
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

- 安装frida  
推荐版本frida 12.8.0版本  

```
pip install frida==12.8.0 
pip install frida-tools==5.3.0 
pip install objection==1.8.4

查看是否安装成功
frida --version
frida-ps --version
```

>请确保你的Android设备已经完成root操作

下载frida-server
查询手机架构`adb shell getprop ro.product.cpu.abi `
下载对应架构的[frida-server](https://github.com/frida/frida/releases)  

[frida-server-12.8.0-android-arm64.xz](https://github.com/frida/frida/releases/download/12.8.0/frida-server-12.8.0-android-arm64.xz)  

```
1.下载frida-server
2.解压frida-server
3.重命名
mv frida-server-12.8.0-android-arm64 fs1280arm64
4.push到手机
adb push  fs1280arm64 /data/local/tmp
5.启动frida-server
adb shell 
su
cd /data/local/tmp
chmod 777 fs1280arm64
./fs1280arm64 -l 0.0.0.0:8888
6.进行通信 ip为手机的局域网ip
frida-ps -H 192.168.99.217:8888
```

test















