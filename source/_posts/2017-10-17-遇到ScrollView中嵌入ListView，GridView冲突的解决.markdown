---
layout: post
description: more
title: 遇到ScrollView中嵌入ListView，GridView冲突的解决
author: shaomiao
header-img: img/post-bg-android.jpg
tags:
  - android开发
  - android笔记
categories:
  - - 开发
    - 移动开发
    - android开发
abbrlink: 2092900669
date: 2017-10-21 00:00:00
---
@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE >> 2,
				MeasureSpec.AT_MOST);
		super.onMeasure(widthMeasureSpec, expandSpec);
	}

参考文章
http://blog.csdn.net/wangbf_java/article/details/60151965
