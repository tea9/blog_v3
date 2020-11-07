---
title: android 信息泄露
abbrlink: 3540884968
date: 2019-08-01 10:09:37
tags:
  - android安全
  - app漏洞
  - android信息泄露
categories:
  - - 安全
    - 移动安全
    - android安全
    - android漏洞
description: android信息泄露
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
---
## ContentProvider目录遍历

> 该漏洞由于Content Provider组件暴露，没有对Content Provider组件访问权限进行限制且对Uri路径没有进行过滤，攻击者通过Content Provider实现的OpenFile接口进行攻击，如通过”../”的方式访问任意的目录文件，造成隐私泄露。  


[7.3 ContentProvider目录遍历漏洞检测](http://01hackcode.com/wiki/7.3)  

## 本地存储泄露信息
