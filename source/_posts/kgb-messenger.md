---
title: KGB Messenger 解题
tags:
  - android安全
categories:
  - - 安全
    - 移动安全
    - android安全
description: KGB Messenger 解题
abbrlink: 1281537923
date: 2021-07-07 16:22:01
---

最近发现一个有趣的题目KGB Messenger然后试着做下。

题目链接：
[kgb_messenger](https://github.com/tlamb96/kgb_messenger)

大佬的解题步骤：
[安卓逆向学习 之 KGB Messenger的writeup（1)](https://blog.csdn.net/weixin_38109420/article/details/116609430)
[安卓逆向学习 之 KGB Messenger的writeup（2)](https://blog.csdn.net/weixin_38109420/article/details/116764373)
[安卓逆向学习之KGBMessenger的writeup（3）](https://blog.csdn.net/weixin_38109420/article/details/117091277)

工具：
jadx
frida

题目：
Challenges
你是国际秘密情报局的逆向工程师。今天早上，你的团队负责人指派你检查一个有问题的APP。据传有个特工斯特林·阿切尔曾与一些克格勃间谍接触并使用了这个APP。你的工作是对这个APP进行逆向，以核实谣言。
Alerts(Medium)
当我们app时，app总是给我们这些讨厌的警报。我们应该调查。
Login (Easy)
这是一个侦察挑战。密码中的所有字符都是小写的。
Social Engineering (Hard)
看来有人不善于保守秘密。他们可能容易受到社会工程的影响。我该说什么？

总共有3个地方需要解决
1.alert弹框
2.成功登录
3.社会工程？

---
解题：
**Alerts(Medium)**
1.安装app打开会弹出This app can only run on Russian devices. 无法正常进入app
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/48/1.png)
使用jadx打开app进行反编译，搜索该字符串
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/48/2.png)
分析代码逻辑直接hook System.getProperty("user.home")=Russia就可以进行绕过
frida代码
```
function main(){
    Java.perform(function(){
        Java.use("java.lang.System").getProperty.overload('java.lang.String').implementation = function(arg1){
            console.log(arg1)
            return "Russia";
        }
    })
}
setImmediate(main);
//frida -U -f com.tlamb96.spetsnazmessenger --no-pause -l kgb-messenger.js
```
绕过之后还有个弹框提示Must be on the user whitelist. 搜索关键字
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/48/3.png)
```
...
public void onCreate(Bundle bundle) {
        super.onCreate(bundle);
        setContentView((int) R.layout.activity_main);
        String property = System.getProperty("user.home");
        String str = System.getenv("USER");
        if (property == null || property.isEmpty() || !property.equals("Russia")) {
            m4456a("Integrity Error", "This app can only run on Russian devices.");
        } else if (str == null || str.isEmpty() || !str.equals(getResources().getString(R.string.User))) {
            m4456a("Integrity Error", "Must be on the user whitelist.");
        } else {
            C0000a.m0a(this);
            startActivity(new Intent(this, LoginActivity.class));
        }
    }
...
```
分析代码同理可得System.getenv这个函数等于R.string.User就可以了，然后去找R.string.User的值
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/48/4.png)
<string name="User">RkxBR3s1N0VSTDFOR180UkNIM1J9Cg==</string>
frida代码
```
Java.use("java.lang.System").getenv.overload('java.lang.String').implementation = function(arg1){
            console.log(arg1)
            return "RkxBR3s1N0VSTDFOR180UkNIM1J9Cg==";
        }
```
绕过两个弹框就出现了登录界面
RkxBR3s1N0VSTDFOR180UkNIM1J9Cg==这个base64是第一个flag FLAG{57ERL1NG_4RCH3R}
使用[cyberchef](https://blogz.gitee.io/cyberchef)进行解密

---

**Login (Easy)**
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/48/5.png)
随便输入登录会提示User not recognized.搜索字符串到登录的Activity
```
 public void onLogin(View view) {
        EditText editText = (EditText) findViewById(R.id.login_username);
        EditText editText2 = (EditText) findViewById(R.id.login_password);
        this.f2542n = editText.getText().toString();
        this.f2543o = editText2.getText().toString();
        if (this.f2542n != null && this.f2543o != null && !this.f2542n.isEmpty() && !this.f2543o.isEmpty()) {
            if (!this.f2542n.equals(getResources().getString(R.string.username))) {
                Toast.makeText(this, "User not recognized.", 0).show();
                editText.setText("");
                editText2.setText("");
            } else if (!m4455j()) {
                Toast.makeText(this, "Incorrect password.", 0).show();
                editText.setText("");
                editText2.setText("");
            } else {
                m4454i();
                startActivity(new Intent(this, MessengerActivity.class));
            }
        }
    }
```
分析代码逻辑克制username是R.string.username的值
在strings.xml找到<string name="username">codenameduchess</string>
使用命令进行输入
```
adb shell input text codenameduchess 	
```
密码在m4455j方法里
```
private boolean m4455j() {
        String str = "";
        for (byte b : this.f2541m.digest(this.f2543o.getBytes())) {
            str = str + String.format("%x", new Object[]{Byte.valueOf(b)});
        }
        return str.equals(getResources().getString(R.string.password));
    }
     this.f2541m = MessageDigest.getInstance("MD5");
```
分析代码可知密码是输入值进行md5后等于R.string.password
找到<string name="password">84e343a0486ff05530df6c705c8bb4</string>
在进行反md5
在网上直接可以搜到值
guest
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/48/6.png)
登录成功后会弹出第2个flag
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/48/7.png)
---
**Social Engineering (Hard)**
会发现有个对话框
随便输入什么没有回复，猜测是要回复某些特定的内容才可以
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/48/8.png)
找到MessengerActivity类onSendMessage方法有flag字段
分析代码
```
...
public void onSendMessage(View view) {
        EditText editText = (EditText) findViewById(R.id.edittext_chatbox);
        String obj = editText.getText().toString();
        if (!TextUtils.isEmpty(obj)) {
            this.f2547o.add(new C0829a(R.string.user, obj, m4460j(), false));
            this.f2546n.mo2625c();
            if (m4457a(obj.toString()).equals(this.f2548p)) {
                Log.d("MessengerActivity", "Successfully asked Boris for the password.");
                this.f2549q = obj.toString();
                this.f2547o.add(new C0829a(R.string.boris, "Only if you ask nicely", m4460j(), true));
                this.f2546n.mo2625c();
            }
            if (m4458b(obj.toString()).equals(this.f2550r)) {
                Log.d("MessengerActivity", "Successfully asked Boris nicely for the password.");
                this.f2551s = obj.toString();
                this.f2547o.add(new C0829a(R.string.boris, "Wow, no one has ever been so nice to me! Here you go friend: FLAG{" + m4459i() + "}", m4460j(), true));
                this.f2546n.mo2625c();
            }
            this.f2545m.mo2466b(this.f2545m.getAdapter().mo2610a() - 1);
            editText.setText("");
        }
    }
...
private String m4457a(String str) {
        char[] charArray = str.toCharArray();
        for (int i = 0; i < charArray.length / 2; i++) {
            char c = charArray[i];
            charArray[i] = (char) (charArray[(charArray.length - i) - 1] ^ '2');
            charArray[(charArray.length - i) - 1] = (char) (c ^ 'A');
        }
        return new String(charArray);
    }
....
private String f2548p = "V@]EAASB\u0012WZF\u0012e,a$7(&am2(3.\u0003";
```
分析代码可得输入的值经过m4457a方法后需要等于f2548p 然后需要反运算f2548p的值
分析m4457a代码charArray前一半异或了2 后一半从最后一位开始异或了A
直接复制java代码在在线平台运行[代码在线平台](https://tool.lu/coderunner/)
java代码
```
class Untitled {
	public static void main(String[] args) {
		System.out.println("hello https://tool.lu/");
		String str="V@]EAASB\u0012WZF\u0012e,a$7(&am2(3.\u0003";
		 char[] charArray = str.toCharArray();
        for (int i = 0; i < charArray.length / 2; i++) {
            char c = charArray[i];
            charArray[i] = (char) (charArray[(charArray.length - i) - 1] ^ 'A');
            charArray[(charArray.length - i) - 1] = (char) (c ^ '2');
        }
		System.out.println( new String(charArray));
	}
}
```
输出Boris, give me the password
```
adb shell 
input text "Boris, give me the password"
```
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/48/9.png)
然后分析获得flag的方法
```
···
 private String m4458b(String str) {
        char[] charArray = str.toCharArray();
        for (int i = 0; i < charArray.length; i++) {
            charArray[i] = (char) ((charArray[i] >> (i % 8)) ^ charArray[i]);
        }
        for (int i2 = 0; i2 < charArray.length / 2; i2++) {
            char c = charArray[i2];
            charArray[i2] = charArray[(charArray.length - i2) - 1];
            charArray[(charArray.length - i2) - 1] = c;
        }
        return new String(charArray);
    }
···
 private String f2550r = "\u0000dslp}oQ\u0000 dks$|M\u0000h +AYQg\u0000P*!M$gQ\u0000";
...
```

分析算法
输入的值先进行右移坐标除以8然后在自己异或 然后在进行位置倒序
进行爆破
python 获取字符
```
>>> import string
>>> string.printable
'0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~ \t\n\r\x0b\x0c'
```
java代码：
```

public class Test {
    public static void main(String [] args){
        String key = "\000dslp}oQ\000 dks$|M\000h +AYQg\000P*!M$gQ\000";
        search(key);

    }

    private static void search(String paramString) {
        char [] charArray = paramString.toCharArray();
        for (int i2 = 0; i2 < charArray.length / 2; i2++) {
            char c = charArray[i2];
            charArray[i2] = charArray[(charArray.length - i2) - 1];
            charArray[(charArray.length - i2) - 1] = c;
        }
        String test = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!\"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~ \t\n\r";
        char [] arrayOftest = test.toCharArray();
        char [] arrayOfparam = charArray;
        for(int i = 0 ; i < arrayOfparam.length; i++)
        {
            for(int j = 0; j < arrayOftest.length ; j++ ){
                char c = arrayOftest[j];
                c = (char)(char)((c >> (i % 8) )^ c);
                if(c == arrayOfparam[i]){
                    System.out.print(arrayOftest[j]);
                    break;
                }
            }
        }
    }



}
```
执行结果 aay I *PaEASE* have the aassworda
输入后得到flag
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/48/10.png)

