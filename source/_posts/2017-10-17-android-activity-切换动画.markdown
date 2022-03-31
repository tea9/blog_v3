---
layout: post
title: android-activity-切换动画
author: shaomiao
header-img: img/post-bg-android.jpg
tags:
  - android开发
  - android笔记
categories:
  - - 开发
    - 移动开发
    - android开发
abbrlink: 2122882361
date: 2017-10-21 00:00:00
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
description: android-activity-切换动画
---
Intent intent = new Intent(this, SecondWindowTransActivity.class);startActivity(intent,        ActivityOptions.makeSceneTransitionAnimation(this).toBundle());
支出api21以后

http://blog.csdn.net/huachao1001/article/details/51659963

转载请注明出处：[【huachao1001的专栏：http://blog.csdn.net/huachao1001】](http://blog.csdn.net/huachao1001)
