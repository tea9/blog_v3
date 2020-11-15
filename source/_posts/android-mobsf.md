---
title: android-mobsf
abbrlink: 1845584916
date: 2020-11-10 10:41:47
tags:
- mobsf
description: mobsf扫描android app
categories:
  - - 移动安全
    - android安全
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
---

## 安装
```
docker pull opensecurity/mobile-security-framework-mobsf
docker run -it -d --name mobsf  -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest
```
访问就可以看到了  
http://127.0.0.1:8000/
