---
layout: post
description: more
title: 记java-lang-IndexOutOfBoundsException--Inconsistency-detected--Invalid-
author: shaomiao
header-img: img/post-bg-android.jpg
tags:
  - android开发
  - android笔记
categories:
  - - 开发
    - 移动开发
    - android开发
abbrlink: 492083186
date: 2017-10-21 00:00:00
---
http://blog.csdn.net/sbsujjbcy/article/details/46474633

	private boolean mIsRefreshing=false;
	mRecyclerView.setOnTouchListener(
		new View.OnTouchListener() {
		  @Override
		  public boolean onTouch(View v, MotionEvent event) {
			if (mIsRefreshing) {
			  return true;
			} else {
			  return false;
			}
		  }
		}
	);
	//当刷新时设置
	//mIsRefreshing=true;
	//刷新完毕后还原为false
	//mIsRefreshing=false
