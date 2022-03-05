---
title: android UnCrackable
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
abbrlink: 2623200361
date: 2021-03-27 14:39:18
tags:
---
# android UnCrackable练习

OWASP的crackme练习里面有一些app安全的保护，破解者需要掌握一些逆向的知识才能获取正确的值。下面是android的两个题目，能帮助掌握基本的jadx逆向java代码、frida和ida逆向so的使用。

样本地址：
Github：https://github.com/OWASP/owasp-mstg/tree/master/Crackmes
百度网盘地址：
链接: https://pan.baidu.com/s/1YCiUU2Xy2xBSUQNxric8mQ  密码: 81kn

## 我用到的环境和工具
- pixel xl arm64-v8a
- python 3.8.0
- frida 12.8.0
- java 11.0.8
- jadx 1.1.0
- IDA 7.0
- Ghidra 9.1.2

## Android Level 1
[UnCrackable1下载](https://raw.githubusercontent.com/OWASP/owasp-mstg/master/Crackmes/Android/Level_01/UnCrackable-Level1.apk)

考察的知识点：
java逆向、root检测绕过
工具：
[jadx](https://github.com/skylot/jadx/releases)、[frida](https://github.com/frida/frida/releases)

安装应用
```
owasp-mstg/Crackmes/Android/Level_01(master*) » adb install UnCrackable-Level1.apk 
Performing Streamed Install
Success
```
打开应用会提示root
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/1.png)
要绕root
用jadx打开apk 搜索root文本 
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/2.png)
发现c.a\c.b\c.c方法是判断root然后进入到方法里查看（选中方法右键跳到声明）
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/3.png)
```
···
 public void onCreate(Bundle bundle) {
        if (c.a() || c.b() || c.c()) {
            a("Root detected!");
        }
        if (b.a(getApplicationContext())) {
            a("App is debuggable!");
        }
        super.onCreate(bundle);
        setContentView(R.layout.activity_main);
    }
···
```
跳到方法发现这个类是检测root的类，使用frida进行hook掉检测类
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/4.png)
```
package sg.vantagepoint.a;

import android.os.Build;
import java.io.File;

public class c {
    public static boolean a() {
        for (String file : System.getenv("PATH").split(":")) {
            if (new File(file, "su").exists()) {
                return true;
            }
        }
        return false;
    }

    public static boolean b() {
        String str = Build.TAGS;
        return str != null && str.contains("test-keys");
    }

    public static boolean c() {
        for (String file : new String[]{"/system/app/Superuser.apk", "/system/xbin/daemonsu", "/system/etc/init.d/99SuperSUDaemon", "/system/bin/.ext/.su", "/system/etc/.has_su_daemon", "/system/etc/.installed_su_daemon", "/dev/com.koushikdutta.superuser.daemon/"}) {
            if (new File(file).exists()) {
                return true;
            }
        }
        return false;
    }
}
```

首先通过逆向分析发现sg.vantagepoint.a.c的类是检测root的类
c类下的a、b、c方法是检测的方法hook方法让他返回值为false
frida代码：
```
Java.perform(function () {
        send("hook start");
        var c=Java.use("sg.vantagepoint.a.c");
        //返回值改成false
        c.a.overload().implementation = function(){
            return false;
        }
        c.b.overload().implementation = function(){
            return false;
        }
        c.c.overload().implementation = function(){
            return false;
        } 
        send("hook end");
    });
```
执行frida代码`frida -U -f  owasp.mstg.uncrackable1 --no-pause -l uncrackable1.js `
启动后已经hook成功了，没有弹框了，然后是随便输入个值在点击VERIFY，提示That's not it.Try again.
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/5.png)
然后我们在jadx反编译后的代码里搜索下Try again
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/6.png)
发现跳转verify方法，用于验证的是a.a(obj)跳转到a方法
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/7.png)
1可以继续用frida hook该函数进行返回值输出

```
public static boolean a(String str) {
        byte[] bArr;
        byte[] bArr2 = new byte[0];
        try {
            bArr = sg.vantagepoint.a.a.a(b("8d127684cbc37c17616d806cf50473cc"), Base64.decode("5UJiFctbmgbDoLXmpL12mkno8HT4Lv8dlat8FxR2GOc=", 0));
        } catch (Exception e) {
            Log.d("CodeCheck", "AES error:" + e.getMessage());
            bArr = bArr2;
        }
        return str.equals(new String(bArr));
    }
```
frida代码：
```
var a =Java.use("sg.vantagepoint.a.a");
a.a.overload('[B', '[B').implementation=function(arg1,arg2){
            //执行函数
            var ret = this.a(arg1,arg2);
            //输出返回值
            console.log(jhexdump(ret));
            return ret;
        }
```
uncrackable1.js
frida完整代码：
```
// owasp.mstg.uncrackable1 
// hookroot检测 
function hookrootuncrackable1(){
    Java.perform(function () {
        send("hook start");
        var c=Java.use("sg.vantagepoint.a.c");
        //返回值改成false
        c.a.overload().implementation = function(){
            return false;
        }
        var a =Java.use("sg.vantagepoint.a.a");
        /**
         *重载报错 根据报错把overload添加上就可以了
         * Error: a(): argument count of 0 does not match any of:
	.overload('[B', '[B')
    at throwOverloadError (frida/node_modules/frida-java-bridge/lib/class-factory.js:1020)
    at frida/node_modules/frida-java-bridge/lib/class-factory.js:686
    at /uncrackable1.js:13                                                      
    at frida/node_modules/frida-java-bridge/lib/vm.js:11
    at E (frida/node_modules/frida-java-bridge/index.js:346)
    at frida/node_modules/frida-java-bridge/index.js:332
    at input:1
         */
        a.a.overload('[B', '[B').implementation=function(arg1,arg2){
            //执行函数
            var ret = this.a(arg1,arg2);
            console.log(jhexdump(ret));
            // console.log(byte2string(ret));
            /***
             * retval = this.a(arg1, arg2);
            password = ''
            for(i = 0; i < retval.length; i++) {
               password += String.fromCharCode(retval[i]);
            }
            console.log("[*] Decrypted: " + password);
             */
            return ret;
        }


        send("hook end");
    });
}
function jhexdump(array,off,len) {
    var ptr = Memory.alloc(array.length);
    for(var i = 0; i < array.length; ++i)
        Memory.writeS8(ptr.add(i), array[i]);
    //console.log(hexdump(ptr, { offset: off, length: len, header: false, ansi: false }));
    console.log(hexdump(ptr, { offset: 0, length: array.length, header: false, ansi: false }));
}

function main(){
    hookrootuncrackable1();
}
setImmediate(main)
// owasp.mstg.uncrackable1
//frida -U -f  owasp.mstg.uncrackable1 --no-pause -l uncrackable1.js 
```

执行结果：
```
~ » frida -U -f  owasp.mstg.uncrackable1 --no-pause -l uncrackable1.js                   
     ____
    / _  |   Frida 12.8.0 - A world-class dynamic instrumentation toolkit
   | (_| |
    > _  |   Commands:
   /_/ |_|       help      -> Displays the help system
   . . . .       object?   -> Display information about 'object'
   . . . .       exit/quit -> Exit
   . . . .
   . . . .   More info at https://www.frida.re/docs/home/
Spawned `owasp.mstg.uncrackable1`. Resuming main thread!                
[Google Pixel XL::owasp.mstg.uncrackable1]-> message: {'type': 'send', 'payload': 'hook start'} data: None
message: {'type': 'send', 'payload': 'hook end'} data: None
7062ac63b0  49 20 77 61 6e 74 20 74 6f 20 62 65 6c 69 65 76  I want to believ
7062ac63c0  65                                               e
```
I want to believe 是最后的值
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/9.png)

2.分析代码使用aes算法进行输出
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/8.png)
```
 » echo 5UJiFctbmgbDoLXmpL12mkno8HT4Lv8dlat8FxR2GOc= | openssl enc -aes-128-ecb -base64 -d -nopad -K 8d127684cbc37c17616d806cf50473cc
I want to believe%  
```

解题参考链接：
https://www.codemetrix.io/hacking-android-apps-with-frida-2/
翻译[【技术分享】利用FRIDA攻击Android应用程序（二）](https://www.anquanke.com/post/id/85759)

## Android Level 2
[UnCrackable2下载](https://raw.githubusercontent.com/OWASP/owasp-mstg/master/Crackmes/Android/Level_02/UnCrackable-Level2.apk)
考察的知识点：
java逆向、root检测绕过、so逆向
工具：[jadx](https://github.com/skylot/jadx/releases)、[frida](https://github.com/frida/frida/releases)、IDA、[Ghidra](https://github.com/NationalSecurityAgency/ghidra)


安装应用：`adb install UnCrackable-Level2.apk `
发现仍然有root检测
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/10.png)
用jadx打开UnCrackable-Level2.apk分析下代码吧~ `jadx-gui UnCrackable-Level2.apk`
依旧搜索`Root detected!`
发现跟第一道题是一样的，就是类名换掉了，用frida在hook下，就可以解决了
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/11.png)
frida代码：
```
Java.perform(function(){
        var b=Java.use("sg.vantagepoint.a.b");
        b.a.overload().implementation = function(){
            return false;
        }
        b.b.overload().implementation = function(){
            return false;
        }
        b.c.overload().implementation = function(){
            return false;
        }
    });
```
正常启动后再搜索`Try again`
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/12.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/13.png)
```
verify()
···
 if (this.m.a(obj)) {
            create.setTitle("Success!");
            str = "This is the correct secret.";
        } else {
            create.setTitle("Nope...");
            str = "That's not it. Try again.";
        }
···
```
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/14.png)
```
private native boolean bar(byte[] bArr);

    public boolean a(String str) {
        return bar(str.getBytes());
    }
```
最后分析到a方法最后到了bar方法是native函数里，是写在so里的，需要用IDA进行分析
解压UnCrackable-Level2.apk
到Level_02/UnCrackable-Level2/lib/armeabi-v7a文件夹中有个libfoo.so文件用IDA打开进行分析
搜索bar函数
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/15.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/16.png)
按F5查看伪c代码
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/17.png)
发现有个`Thanks for all the fish`字符串尝试输入，发现已经Success了
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/18.png)

除了IDA还可以用Ghidra打开尝试
搜索bar函数 打开
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/19.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/20.png)
分析代码发现有几个值进行对比
然后拼接后转字符串查看 小端序所以是倒着的
`6873696620656874206c6c6120726f6620736b6e616854`
使用十六进制转换工具进行查看
十六进制转换工具：https://zixuephp.net/tool-str-hex.html
选择16进制转字符串就可以了
工具：https://gchq.github.io/CyberChef
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/46/21.png)
得到的结果`hsif eht lla rof sknahT`
最后的结果`Thanks for all the fish`


解题参考链接：
https://tereresecurity.wordpress.com/2021/03/23/write-up-uncrackable-level-2/

https://enovella.github.io/android/reverse/2017/05/20/android-owasp-crackmes-level-2.html

https://www.codemetrix.io/hacking-android-apps-with-frida-3/
翻译[【技术分享】利用FRIDA攻击Android应用程序（三）](https://www.anquanke.com/post/id/85996)

## Android Level 3
解题参考链接：
https://enovella.github.io/android/reverse/2017/05/20/android-owasp-crackmes-level-3.html

http://sh3llc0d3r.com/owasp-uncrackable-android-level3/
翻译[【技术分享】利用FRIDA攻击Android应用程序（四）](https://www.anquanke.com/post/id/86201)

## Android Level 4
https://www.romainthomas.fr/post/20-09-r2con-obfuscated-whitebox-part1/
https://www.romainthomas.fr/post/20-09-r2con-obfuscated-whitebox-part2/


## LINKS
[[原创]UnCrackable App 三部曲学习记录分享](https://bbs.pediy.com/thread-255361.htm)
[OWASP Android Uncrackable1~3练习](https://xz.aliyun.com/t/6819#toc-0)