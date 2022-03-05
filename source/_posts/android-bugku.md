---
title: android bugku ctf mobile做题过程记录
abbrlink: 2507273112
date: 2021-04-06 16:32:26
tags:
  - android安全
  - android ctf
categories:
  - - 安全
    - 移动安全
    - android安全
description: android bugku ctf mobile做题过程记录
---

# android bugku做题过程记录
## 工具

IDA、jadx、frida

## 入门逆向 

https://ctf.bugku.com/challenges/detail/id/99.html
过程：用IDA打开，找到main函数，用R转换
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/1.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/2.png)

最后得到flag{Re_1s_S0_C0OL}

## mobile1(gctf)
https://ctf.bugku.com/challenges/detail/id/137.html

安装apk
```
adb install gctf_mobile1.apk 
```
打开TopCtf
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/3.png)
随便在输入框中输入，返回错误！
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/4.png)
用jadx打开apk分析代码
打开MainActivity，有个checkSN方法是验证方法
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/5.png)
```
 public void onClick(View v) {
                if (!MainActivity.this.checkSN(MainActivity.this.edit_userName.trim(), MainActivity.this.edit_sn.getText().toString().trim())) {
                    Toast.makeText(MainActivity.this, R.string.unsuccessed, 0).show();
                    return;
                }
                Toast.makeText(MainActivity.this, R.string.successed, 0).show();
                MainActivity.this.btn_register.setEnabled(false);
                MainActivity.this.setTitle(R.string.registered);
            }
```
点进去查看方法,大概分析sn需要22位，进行了md5加密for循环改变了下位置
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/6.png)
```
 public boolean checkSN(String userName, String sn) {
        if (userName == null) {
            return false;
        }
        try {
            if (userName.length() == 0 || sn == null || sn.length() != 22) {
                return false;
            }
            MessageDigest digest = MessageDigest.getInstance("MD5");
            digest.reset();
            digest.update(userName.getBytes());
            String hexstr = toHexString(digest.digest(), "");
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < hexstr.length(); i += 2) {
                sb.append(hexstr.charAt(i));
            }
            if (("flag{" + sb.toString() + "}").equalsIgnoreCase(sn)) {
                return true;
            }
            return false;
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
            return false;
        }
    }
```
我用的方法先hook toHexString方法，得到结果在进行for循环隔位取
完整frida hook代码
gctf.js
```
function hook_toHexString(){
    Java.perform(function(){
        var MainActivity=Java.use("com.example.crackme.MainActivity");
        MainActivity.toHexString.implementation=function(arg1,arg2){
            var result = this.toHexString(arg1, arg2);
            console.log(result);
            return result;  
        }
    })
}
function main(){
    hook_toHexString();
}
setImmediate(main)
//com.example.crackme
//frida -U -f com.example.crackme --no-pause -l gctf.js  
//b9c77224ff234f27ac6badf83b855c76
//bc72f242a6af3857
//flag{bc72f242a6af3857}

/****
 * public class HelloWorld {
    public static void main(String []args) {
		
       System.out.println("Hello World!");
		System.out.println("b9c77224ff234f27ac6badf83b855c76".toString());
		String hexstr ="b9c77224ff234f27ac6badf83b855c76";
		StringBuilder sb = new StringBuilder();
		for (int i = 0; i < hexstr.length(); i += 2) {
                sb.append(hexstr.charAt(i));
            }
		System.out.println(sb.toString());
    }
}

Hello World!
b9c77224ff234f27ac6badf83b855c76
bc72f242a6af3857

flag{bc72f242a6af3857}
 * 
 */
```
运行frida -U -f com.example.crackme --no-pause -l gctf.js  
[Google Pixel XL::com.example.crackme]-> b9c77224ff234f27ac6badf83b855c76
用[在线java运行工具](https://c.runoob.com/compile/10)执行 隔位取操作
java
```
public class HelloWorld {
    public static void main(String []args) {
		String hexstr ="b9c77224ff234f27ac6badf83b855c76";
		StringBuilder sb = new StringBuilder();
		for (int i = 0; i < hexstr.length(); i += 2) {
                sb.append(hexstr.charAt(i));
            }
		System.out.println(sb.toString());
    }
}
//bc72f242a6af3857
```
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/7.png)
得到结果bc72f242a6af3857，最后拼上flag{bc72f242a6af3857}

## mobile2(gctf)
https://ctf.bugku.com/challenges/detail/id/138.html

解压后是以下文件

```
mobile2(gctf) » ls                                  
AndroidManifest.xml assets              res
META-INF            classes.dex         resources.arsc
```
经提示后，flag在AndroidManifest.xml里用010Editor打开查看
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/8.png)

最后得到flag{8d6efd232c63b7d2}

## First_Mobile(xman)

https://ctf.bugku.com/challenges/detail/id/139.html

安装

```bash
adb install 07bfacf2-82df-4eab-8b41-a34aa7486c5a.apk
```

运行随便输入点SUBMIT直接崩溃了 用jadx打开直接看反编译后的代码

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/9.png)

```
adx-gui 07bfacf2-82df-4eab-8b41-a34aa7486c5a.apk 
```

查看MainActivity有个check的方法

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/10.png)

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/11.png)

分析encode类下的check代码，编写一个python脚本进行爆破

[python3在线运行工具](https://c.runoob.com/compile/9)

```
#!/usr/bin/python
print("Hello, World!");
import time
coretu = [23,22,26,26,25,25,25,26,27,28,30,30,29,30,32,32]
print('XMAN{',end='')
for indexnum,i in enumerate(coretu):
    keynum =33
    while 1:
        nowb = (keynum+i)%61
        sss = chr(abs(((nowb*2)-indexnum)))
        #time.sleep(0.1)
        #print chr(keynum)
        if sss == chr(keynum%128):
            print(sss,end='')
            break
        else:
            keynum+=1
print('}',end='')

a = [23, 22, 26, 26, 25, 25, 25, 26, 27, 28, 30, 30, 29, 30, 32, 32]
key = ''
for m in range(16):
    for i in range(128):
        k = i
        k = (k + a[m]) % 61
        k = k * 2 - m
        if k == i:
            print(f"[*]第{m+1}位是{k}")
            key = key + chr(k)
            break
print("XMAN{" + key + "}")
k == ((k + a[m]) % 61)*2-m 
```

最后的结果XMAN{LOHILMNMLKHILKHI}

## HelloSmali2

```
解压后有个XMan.java 
HelloSmali2 (1) » ls                           
XMan.java
f45775643c-46846-5990-b3793-32e8ecd15f0d.smali
```

解压后有个XMan.java 在[在线java运行平台](https://c.runoob.com/compile/10)上运行就出来了

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/47/12.png)

最后结果flag{eM_5m4Li_i4_Ea5y}



## LINKS

题目链接: https://pan.baidu.com/s/1VpS_aew4BdLrheqBL0X_cg  密码: 27e0

