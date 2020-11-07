---
layout: post
title: github上fork了别人的项目后，再同步更新别人的提交
categories:
  - - 开发
    - git
tags: 
  - git
abbrlink: 2129490429
date: 2018-06-25 00:00:00
description: 马克
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
---

[github上fork了别人的项目后，再同步更新别人的提交](https://blog.csdn.net/qq1332479771/article/details/56087333)


	
	git remote -v
	git remote add upstream aaa
	git remote -v
	git remote remove upstream

	4.重新添加源代码库
	git remote add upstream <fork link>
	git remote -v

	5.从源仓库更新同步代码
	git fetch upstream

	6.合并到本地代码库
	git merge upstream/master

	7.拉取远程代码库
	git pull origin master

	8.更新远程代码库
	git push 
