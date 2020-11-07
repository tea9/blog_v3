---
layout: post
title: URL中的大小写敏感
categories:
  - - 安全
    - web安全
tags: 
  - tips
abbrlink: 1353455041
date: 2018-10-15 00:00:00
description: url中的大小写敏感
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
---

## URL中的大小写敏感

	http://fred:wilma@www.example.com/private.asp?doc=3&part=4#footer

1. 协议标示符 （http/https） 大小写不敏感 HTTP、http、hTtP  

2. 用户ID和密码 （fred和wilma）可能大小写敏感 取决于你的服务器软件  

3. 机器的名称（www.example.com） 不是大小写敏感 www.eXamplE.coM 或者其他大小写组合  

4. 获取资源情况 （private.asp） ASP是Windows动态服务器扩展名 Windows服务器都不是大小写敏感的，因此/PRIvate.aSP可能也有效  
