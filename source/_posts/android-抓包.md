---
title: android app 抓包
tags:
  - android安全测试
  - 抓包
  - android安全
  - burpsuite
  - postern
description: android app抓包
categories:
  - - 安全
    - 移动安全
    - android安全
    - app抓包
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
date: 2021-03-02 15:22:05
---

## BurpSuite抓包

### burp安装：
可以参考国光大佬的文章
[BurpSuite Pro 2020.11.3 For Windows](https://www.sqlsec.com/2020/10/winbp.html)  
[macOS 下如何优雅的使用 Burp Suite (2020.11.3)](https://www.sqlsec.com/2019/11/macbp.html)  


### 设置代理：
burp拦截禁用先关掉
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/3.png)
打开burpsuite-proxy-options-add
添加代理
先ifconfig查看ip地址 在burp中找到对应的ip
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/1.png)
手机设置
连接wifi
长按修改网络-高级选项-代理-手动 配置主机名 端口
主机名填写
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/2.png)
配置后浏览器访问
http://ip111.cn/
然后看burp上是否有ip111的抓包记录 有的话就代表配置成功
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/4.png)

### 证书安装：
**导出证书**
Proxy-Options-导出CA证书
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/5.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/6.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/7.png)

**传输证书到手机上**
```
adb push cacert.cer /sdcard/Download
```

**安装证书**
以我的手机Pixel为例，选择设置-安全性和位置信息-加密与凭据-从存储设备安装-选择证书
填写证书名称 提示已安装 就安装成功了
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/8.png)
返回选择信任的凭据-用户 有PortSwigger就代表证书安装成功了
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/9.png)

**android7.0以后安装证书**
导出后的证书der转pem格式
```
openssl x509 -inform DER -in cacert.der -out cacert.pem
```

然后adb push到/sdcard/Download文件夹下，然后设置→安全性和位置信息→加密与凭据→从存储设备安装，选择pem证书
拷贝证书到系统证书目录
```
adb shell
su
cd /data/misc/user/0/cacerts-added
ls
9a5ba575.0
mount -o remount,rw /
cp * /etc/security/cacerts/
mount -o remount,ro /

查看
cd /etc/security/cacerts
ls -alit
total 1036
 710 drwxr-xr-x 2 root root 4096 2021-03-02 14:16 .
2568 -rw-r--r-- 1 root root  973 2021-03-02 14:16 9a5ba575.0
```
重启后查看系统证书列表是否存在burp的证书
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/10.png)
打开浏览器测试抓包
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/11.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/12.png)

如果用了magisk框架可以使用magisk movecert插件 移动证书
android ssl证书插件
https://github.com/Magisk-Modules-Repo/movecert
(另外一个插件https://github.com/NVISO-BE/MagiskTrustUserCerts)

burpsuie导入客户端证书
User options -> SSL -> Client SSL Certificate
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/33.png)

---
## Charles抓包
如果抓不到包的情况下
反安卓防抓包--no_proxy
[Android 禁止代理抓包](https://blog.csdn.net/b799841701/article/details/78611766?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.control&dist_request_id=&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.control)

Burp Suite只能对HTTP，HTTPS，websocket进行抓包。如果当APP使用了socket端口进行通信也可以使用postern+charles进行抓包
Postern:
https://github.com/postern-overwal/postern-stuff
Charles:
https://www.charlesproxy.com/
Charles 在线破解工具
https://www.zzzmode.com/mytools/charles/
https://www.charlesproxy.com/download/

Charles抓包配置
**HTTP**
Proxy-Proxy Settings-Port 默认8888
ifconfig查看本机ip
手机配置
设置-WLAN-修改网络
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/15.png)
浏览器访问http://ip111.cn
查看charles是否有抓到的包
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/16.png)
**HTTPS**
在上面两步的基础上，你需要为charles添加SSL Proxiyng。具体的步骤是：Proxy - SSL Proxiyng Settings -勾选“Enable SSL Proxying”，并点击下方的Add，你可以这样配置：
host是你要抓的域名或者ip（这里用通配符`*`，表示抓去所有的https请求），port为443。
下载手机的SSL证书。charles里点击help -SSL Proxying－Save Charles Root Certificate，然后选择目录，保存一个类似于这样的“charles-ssl-proxying-certificate.pem”文件
3）给手机安装证书:  
```
adb push charles-ssl-proxying-certificate.pem /sdcard/Download/charles.pem
```
打开手机设置 -更多设置 -系统安全 -从存储设备安装 -选择charles.pem，点击安装
测试访问https://www.baidu.com
尝试访问
已经可以看到请求包
访问爱奇艺app会提示
Connection established
说明证书不被信任
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/16.png)
拷贝证书到系统证书目录
```
adb shell
su
cd /data/misc/user/0/cacerts-added
ls
9a5ba575.0 af5d8655.0
mount -o remount,rw /
cp * /etc/security/cacerts/
mount -o remount,ro /

查看
cd /etc/security/cacerts
ls -alit
total 1036
 710 drwxr-xr-x 2 root root 4096 2021-03-02 14:16 .
2568 -rw-r--r-- 1 root root  973 2021-03-02 14:16 9a5ba575.0
```
打开爱奇艺app-登录注册-获取验证码
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/18.png)
**SOCKS**
Proxy-Proxy Settings-勾选Enable SOCKS proxy
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/19.png)
Postern设置
配置代理
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/20.png)
配置规则
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/21.png)
打开VPN测试抓包效果，要不wifi代理设置先关掉
选择allow
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/30.png)
**Charles流量转发到BurpSuite**
Charles设置：Proxy-External Proxy Settings-勾选Use external proxy servers-勾选Secure Web Proxy
Secure Web Proxy Server填写127.0.0.1:8080
Burp Suite设置：
Proxy-Options勾选127.0.0.1:8080
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/24.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/25.png)

## 自签名证书校验
启动frida
```
cd /data/local/tmp
./frida-server 
```
**找证书文件**
解包后查找证书文件
一般查找后缀名为p12的文件
```
tree -NCfhl |grep -i p12 
find . -name "*.p12"
```

**找证书密码**
使用肉丝大佬万能脚本[实用FRIDA进阶：内存漫游、hook anywhere、抓包](https://www.anquanke.com/post/id/197657#h2-9)  

```
function hook_KeyStore_load() {
    Java.perform(function () {
        var StringClass = Java.use("java.lang.String");
        var KeyStore = Java.use("java.security.KeyStore");
        KeyStore.load.overload('java.security.KeyStore$LoadStoreParameter').implementation = function (arg0) {
            printStack("KeyStore.load1");
            console.log("KeyStore.load1:", arg0);
            this.load(arg0);
        };
        KeyStore.load.overload('java.io.InputStream', '[C').implementation = function (arg0, arg1) {
            printStack("KeyStore.load2");
            console.log("KeyStore.load2:", arg0, arg1 ? StringClass.$new(arg1) : null);
            this.load(arg0, arg1);
        };

        console.log("hook_KeyStore_load...");
    });
}
```
执行脚本
```
获取包名
frida-ps -U|grep -i xx
执行脚本
frida -U -f  com.xx.xx --no-pause -l xx.js
导出证书
adb shell
cd /sdcard/Download
mv android.content.res.AssetManager\$AssetInputStream@82749d9.p12  soul.p12
adb pull /sdcard/Download/soul.p12
```
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/13.png)
可以看到证书密码已经暴露出来了

**charles导入证书**
导入证书后所有的请求都是使用该证书进行访问的建议抓完包去除

Charles设置：
Proxy-SSL Proxying Settings-Client Certificates
导入证书-输入密码-ip和端口都填* 或者填写对应的ip和端口
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/26.png)
尝试抓包
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/27.png)

- soul
抓包提示网络错误
脚本
```
//打印自签名证书
function hook_KeyStore_load2(){
    Java.perform(function()
    {
        var ByteString = Java.use("com.android.okhttp.okio.ByteString");
        var myArray=new Array(1024);
        var i = 0;
        for(i=0;i<myArray.length;i++){
            myArray[i]=0x0;
        }
        var buffer = Java.array('byte',myArray);

        var StringClass = Java.use("java.lang.String");
        var KeyStore = Java.use("java.security.KeyStore");
        KeyStore.load.overload('java.security.KeyStore$LoadStoreParameter').implementation = function (arg0) {
            console.log(Java.use("android.util.Log").getStackTraceString(Java.use("java.lang.Throwable").$new()));

            console.log("KeyStore.load1:", arg0);
            this.load(arg0);
        };
        KeyStore.load.overload('java.io.InputStream', '[C').implementation = function (arg0, arg1) {
            console.log(Java.use("android.util.Log").getStackTraceString(Java.use("java.lang.Throwable").$new()));

            console.log("KeyStore.load2:", arg0, arg1 ? StringClass.$new(arg1) : null);

            if (arg0){
                var file =  Java.use("java.io.File").$new("/sdcard/Download/"+ String(arg0)+".p12");
                var out = Java.use("java.io.FileOutputStream").$new(file);
                var r;
                while( (r = arg0.read(buffer)) > 0){
                    out.write(buffer,0,r)
                }
                console.log("save success!")
                out.close()
            }
            this.load(arg0, arg1);
        };

        console.log("hook_KeyStore_load...");

    });
}

function hook_ssl() {
    Java.perform(function() {
        var ClassName = "com.android.org.conscrypt.Platform";
        var Platform = Java.use(ClassName);
        var targetMethod = "checkServerTrusted";
        var len = Platform[targetMethod].overloads.length;
        console.log(len);
        for(var i = 0; i < len; ++i) {
            Platform[targetMethod].overloads[i].implementation = function () {
                console.log("class:", ClassName, "target:", targetMethod, " i:", i, arguments);
                //printStack(ClassName + "." + targetMethod);
            }
        }
    });
}

function printStack(str_tag)
 {
    var Exception=  Java.use("java.lang.Exception");
    var ins = Exception.$new("Exception");
    var straces = ins.getStackTrace();

    if (undefined == straces || null  == straces)
    {
        return;
    }

    console.log("==" + str_tag + " Stack strat ===");
    console.log("");

    for (var i = 0; i < straces.length; i++)
    {
        var str = "   " + straces[i].toString();
        console.log(str);
    }

    console.log("");
    console.log("===" + str_tag + " Stack end ===\r\n");
    Exception.$dispose();
 }

function main(){
    hook_KeyStore_load2();
}
setImmediate(main)
```
```
查找包名
pm -l |grep -i soul 
package:cn.soulapp.android
执行脚本
frida -U -f  cn.soulapp.android --no-pause -l xx.js 
```
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/28.png)
已经获取成功了，把证书导入charles抓包
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/29.png)
已经可以抓到包了

- 统一社会信用代码查询
https://ss.cods.org.cn/mobile/download
双向证书校验
使用脚本获取证书和密码
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/31.png)
已经获取到证书
导入证书到charles
发现还无法抓到包sslpinning了

**sslpinning**
使用objection
```
objection -g com.xx.xx explore -s " android sslpinning disable"
```
或者脚本
```
function hook_ssl() {
    Java.perform(function() {
        var ClassName = "com.android.org.conscrypt.Platform";
        var Platform = Java.use(ClassName);
        var targetMethod = "checkServerTrusted";
        var len = Platform[targetMethod].overloads.length;
        console.log(len);
        for(var i = 0; i < len; ++i) {
            Platform[targetMethod].overloads[i].implementation = function () {
                console.log("class:", ClassName, "target:", targetMethod, " i:", i, arguments);
                //printStack(ClassName + "." + targetMethod);
            }
        }
    });
}
```
使用大佬的脚本进行hook ssl
```
function main(){
    
    hook_ssl();
}
setImmediate(main)

frida -U -f  com.ninemax.ncsearchnew --no-pause -l xx.js      
```
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/42/32.png)
已经可以抓到包了

抓包其他工具：
ClashForAndroid
https://github.com/Kr328/ClashForAndroid/releases
brook
https://github.com/txthinking/brook/releases/tag/v20210214
HttpCanary
https://httpcanary.com/zh-hans/install.html
安卓应用层抓包通杀脚本
https://github.com/r0ysue/r0capture

## LINKS
[实用FRIDA进阶：内存漫游、hook anywhere、抓包](https://www.anquanke.com/post/id/197657#h2-9)   
[如何全面的抓取手机流量，避免丢失](http://yearnwang.gitee.io/pusafe/2020/04/26/catch_packet/)
[为你的android App实现自签名的ssl证书（https）](https://blog.csdn.net/u013424496/article/details/51161647/)  
[Frida 学习笔记](https://api-caller.com/2019/03/30/frida-note/#frida-%E7%A0%B4%E8%A7%A3%E7%A4%BA%E4%BE%8B)
[自动定位webview中的SLL_read和SSL_write](https://mabin004.github.io/2020/07/24/%E8%87%AA%E5%8A%A8%E5%AE%9A%E4%BD%8Dwebview%E4%B8%AD%E7%9A%84SLL-read%E5%92%8CSSL-write/)
[安卓应用层抓包通杀脚本发布！](https://bbs.pediy.com/thread-264283.com)  

[Android Https抓包实践](https://mabin004.github.io/2018/03/05/https%E4%B8%AD%E9%97%B4%E4%BA%BA%E6%94%BB%E5%87%BB%E5%8E%9F%E7%90%86/)