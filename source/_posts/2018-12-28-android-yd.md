---
layout: post
title: android yd
categories:
  - - 开发
    - 移动开发
    - android开发
tags: 
  - android开发
abbrlink: 871619508
date: 2018-12-28 00:00:00
description: youdao付费视频导出
---
	

	 /sdcard/Android/data/com.youdao.course/.Courses/

	 先提前把免费视频下载 
	 然后把文件名替换


	 adb pull /sdcard/Android/data/com.youdao.course/.Courses/.c89b26d3a5dd2b9244a284a15169d2601545980518788.yd

	 adb push xx.yd /sdcard/Android/data/com.youdao.course/.Courses/

	c89b26d3a5dd2b9244a284a15169d2601545980518788
	039d67a4c9aee89d7ac7c1410faa69781529575319769


	修改目的： 
	把已经下载的付费视频，提取出来
	步骤：
	首先下载免费的视频，然后把付费下载过的视频 替换成免费的签名 就可以在播放器上继续观看付费视频，并且可以提取出来，在其他手机上观看


	格式大师
	ES文件浏览器
	存储空间清理


	首先下载个视频
	格式大师 查看最新文件
	ES文件浏览器 查看

	adb shell 操作


	adb shell显示隐藏文件

	ls -a
