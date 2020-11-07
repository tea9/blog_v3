---
layout: post
title: 2.1.1-文件上传流动原理与流程-上传检测流程
category: 
  - web安全
tags: 
  - web安全
  - 文件上传漏洞
abbrlink: 2419343777
date: 2018-07-19 00:00:00
---

## 文件上传检测流程

![文件上传检测流程](https://coding.net/u/tea9/p/image/git/raw/master/blog_img/15/01.png)

**前端提交：**  
javaScript检测、Flash AS检测  
app：java代码检测  

**数据传输：**  
形成的报文到后端处理 -> 会存在WAF拦截、IPS入侵防御拦截  

**后端处理：**  
文件扩展名检测、MIME TYPE检测（报文文件参数content type）、文件格式检测、内容检测（检测恶意代码）  

**写入文件系统：**  
文件重命名、强制改变后缀名为jpg、杀毒软件查杀（隔离区或删除、00截断） 

**访问文件：**  
无执行权限（直接返回文件内容）、未知位置（根据特殊名或id读取文件）  