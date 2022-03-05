---
title: daily_2021
top: 10
tags:
  - daily
description: 2021年的日记
categories:
  - - 人生初见
    - daily
password: tea2021
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
abbrlink: 2812625272
date: 2021-04-14 10:06:41
---

# DAILY

## 2021年4月19日 学习记录
接码平台：
https://www.zusms.com/
wannengjm.com
http://www.kakasms.com/
deskos.cn
https://yunduanxin.net/

## 2021年4月15日 学习记录 
[whatsapp](https://census-labs.com/news/2021/04/14/whatsapp-mitd-remote-exploitation-CVE-2021-24027/)

## 2021年4月14日 学习记录
**有用的网站：**
[微步在线](https://x.threatbook.cn/)情报分享社区，刚看群里发现还有搞笑段子
如：
```
HW如何体面的维持甲方的“四菜一汤”
 匿名用户 2021-04-14 09:33:01 319次浏览
预警越来越少，已经从刚开始最多的一天40条，减少到了5条以内，每当甲方问我预警数时，眼神中充满期待又略带不安。

为了体面的维持甲方提供的“四菜一汤”

我决定自费采购几台VPS，再弄几个新域名，写几个自动化脚本。。。O(∩_∩)O哈哈~  
```


## 2021年4月13日 学习记录
### seebug
seebug 漏洞分析[seebug知道创宇](https://www.seebug.org/search/?keywords=android+Scheme&category=&level=all)
[Android版Dropbox内容提供程序安全绕过漏洞](https://www.seebug.org/vuldb/ssvid-20862)
### jsop跨域学习
参考链接：
[解决cookie跨域访问](https://www.cnblogs.com/hujunzheng/p/5744755.html)
[jsonp跨域请求详解——从繁至简](https://zhuanlan.zhihu.com/p/24390509)  
[JSONP 劫持漏洞实例](https://www.cnblogs.com/xiaozi/p/9963523.html)
[常见WEB漏洞：JSONP安全与防御](https://zhuanlan.zhihu.com/p/62694920)
读取cookie代码：
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>读取cookie</title>
<script type="text/javascript" src="https://www.w3school.com.cn/jquery/jquery-1.11.1.min.js"></script>
<script type="text/javascript">
	// $(document).ready(function(){
	// 	$.ajax({
	// 		url:'http://www.baidu.com',dataType:"jsonp",jsonp:"jsonpcallback",success:function(data){
	// 			alert(data.res);
	// 		}
	// 	})
	// })

</script>
<script type="text/javascript">
xx();
	function xx(){
		var strCookie = document.cookie;
            var arrCookie = strCookie.split("; ");
            for(var i = 0; i < arrCookie.length; i++){
               var arr = arrCookie[i].split("=");
               console.log(arr[0],arr[1]);
               alert(arr[0],arr[1])
            }
         alert(1)
         alert(strCookie)
	}
</script>
</head>
<body>
    <h1>xxx</h1>
    <p>lll</p>
</body>
</html>
```

### mac中apache默认位置
配置文件
`/etc/apache2/httpd.conf`
目录 DocumentRoot 及下面Directory

Apache运行命令:

sudo apachectl start  //开始
sudo apachectl restart  //重启
sudo apachectl stop  //停止
默认位置在：
/Library/WebServer/Documents

### url schema 问题
[草料二维码](https://cli.im/)  
[activity组件导出实验](http://tea9.xyz/post/2970212528.html)
[android androidmanifest需要检测的安全问题](http://tea9.xyz/post/1654160465.html)  
[tent scheme URL attack](https://zhishihezi.net/b/fd40233b64d33688213db89fb311d5a9#open)
[android scheme链接打开本地应用](https://www.jianshu.com/p/45af72036e58?from=singlemessage)
[Android Intent Scheme URLs攻击](https://www.cnblogs.com/lytwajue/p/6724055.html)
[7.7 Intent Scheme URL漏洞攻击检测](http://01hackcode.com/wiki/7.7)
[IntentScheme](https://www.mbsd.jp/Whitepaper/IntentScheme.pdf)
[细数安卓APP那些远程攻击漏洞](https://paper.seebug.org/papers/Security%20Conf/KCon/2015/%E7%BB%86%E6%95%B0%E5%AE%89%E5%8D%93%20APP%20%E9%82%A3%E4%BA%9B%E8%BF%9C%E7%A8%8B%E6%94%BB%E5%87%BB%E6%BC%8F%E6%B4%9E.pdf)
[0x01 Android Intents with Chrome](https://wooyun.js.org/drops/Intent%20scheme%20URL%20attack.html)
[Android 跨应用间调用: URL Scheme](https://www.jianshu.com/p/7b09cbac1df4)

```
String url = "jafir://main.app?key=传递的参数" 
 Intent in = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
                    startActivity(in);

Intent intent = getIntent();
        String scheme = intent.getScheme();
        Uri uri = intent.getData();
        System.out.println("scheme:"+scheme);
        if (uri != null) {
            String host = uri.getHost();
            String dataString = intent.getDataString();
            //获得参数值
            String key1 = uri.getQueryParameter("key1");
      }  
```

### android webview风险
[android webview file域控制不严格利用](http://tea9.xyz/post/3957115657.html)
[android webview 漏洞](http://tea9.xyz/post/4272460574.html)  
[android XSS攻击](http://tea9.xyz/post/1228473938.html)
[TikTok for Android 1-Click RCE](https://medium.com/@dPhoeniixx/tiktok-for-android-1-click-rce-240266e78105)

### OWASP Mobile Security Testing Guide
[OWASPMobileSecurity](https://mobile-security.gitbook.io/mobile-security-testing-guide/android-testing-guide/0x05a-platform-overview)
[basic-security_testing](https://mobile-security.gitbook.io/mobile-security-testing-guide/android-testing-guide/0x05b-basic-security_testing#information-gathering)
[Mobile AppSec Verification Standard](https://mobile-security.gitbook.io/masvs/security-requirements/0x06-v1-architecture_design_and_threat_modelling_requireme)
[APP 安全测试（OWASP Mobile Top 10）--后篇之一](https://blog.csdn.net/m0_37268841/article/details/104728551)
[[翻译]OWASP 安卓测试指南（v1.2 - 14 May 2020）节选](https://bbs.pediy.com/thread-260849.htm)

### SSL Pinning
[[翻译]移动应用程序 SSL Pinning 安全开发与审计框架：Anddroid 设备案例 ](https://bbs.pediy.com/thread-260658.htm)


## 2021年4月12日 学习记录
[APP调试中容易被忽视的安全项](https://www.freebuf.com/vuls/224998.html)
## gplaycli
gplaycli
GPlayCli是用于从Google Play商店搜索，安装和更新Android应用程序的命令行工具。
https://github.com/matlink/gplaycli#installation
下载
$ gplaycli -p -v -d com.google.android.keep

## KeyStore
[keystore](https://developer.android.com/reference/java/security/KeyStore.html)

## android 逆向教程
[《教我兄弟学Android逆向09 IDA动态破解登陆验证》](https://www.52pojie.cn/thread-742686-1-1.html)