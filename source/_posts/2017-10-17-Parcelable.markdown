---
layout: post
description: more
title: Parcelable
author: shaomiao
header-img: img/post-bg-android.jpg
tags:
  - android开发
  - android笔记
categories:
  - - 开发
    - 移动开发
    - android开发
abbrlink: 2098168253
date: 2017-10-21 00:00:00
---
	public class Book implements Parcelable {

		public int bookId;
		public String bookName;

		public Book(int bookId, String bookName) {
			this.bookId = bookId;
			this.bookName = bookName;
		}

		public int describeContents() {
			return 0;
		}

		public void writeToParcel(Parcel out, int flags) {
			out.writeInt(bookId);
			out.writeString(bookName);
		}

		public static final Parcelable.Creator<Book> CREATOR = new Creator<Book>() {
			@Override
			public Book createFromParcel(Parcel parcel) {
				return new Book(parcel);
			}

			@Override
			public Book[] newArray(int i) {
				return new Book[i];
			}
		};

		private Book(Parcel in) {
			bookId = in.readInt();
			bookName = in.readString();
		}
	}
