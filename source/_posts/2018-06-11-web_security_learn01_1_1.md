---
layout: post
title: 1.1.1-SQL注入-SQL注入基础-Web应用框架分析
categories:
  - - 安全
    - web安全
tags: 
  - sql注入
  - web安全
abbrlink: 2839062402
date: 2018-06-11 00:00:00
description: sql注入
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
---

# SQL注入漏洞原理与利用

## Web应用框架分析
### 01 WEB框架
webapp->  
	web前端  
	web应用CMS／OA／Blog  
	Web开发框架：Django／Struts2/ThinkPHP  
	Web语言：PHP/JSP/.Net  

---

容器->  
	IIS、Apach、TomCat、Nginx


### 02 理解SQL注入
所有SQL语句 都有可能SQL注入  
