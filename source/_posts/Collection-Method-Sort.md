---
title: Collection Method - Sort
date: 2017-06-27 21:45:57
author: Justin
cover: /images/contact-bg.jpg
tags:
categories: 
- Programming
---

### Function 描述

java.util.Collection 提供了sort方法，對List進行排序

> public static &lt; T&gt; void sort(List&lt; T&gt; list, Comparator&lt; ? super T&gt; c)  

由於List有索引，因此才能進行排序

Comparator則允許你以不同的方法對List進行排序

### 實作細節

想對一個物件進行排序，必須先予他一個排序的規則，而方法就是使被排序的物件實作  **java.lang.Comparable** 介面，且實作compareTo()方法，這個方法必須傳入被比較的物件，然後傳回大於0、等於0、小於0的數，以下是一個範例:
```Java
	import java.util.*;

	class Time implements Comparable< Time> {
		private int hour;
		private int minute;
		private int second;

		//Constructor
		Time(int hour, int minute, int second) {
			this.hour = hour;
			this.minute = minute;
			this.second = second;
		}

		@Override
		public int compareTo(Time other) {
			int hourDifference = this.hour - other.hour;

			if(hourDifference != 0)
				return hourDifference;

			int minuteDifference = this.minute - other.minute;

			if(minuteDifference != 0)
				return minuteDifference;

			int secondDifference = this.second - other.second;

			return secondDifference;			
		}
	}
```

若是a.compareTo(b)這樣的一個比較，則傳回值大於0，表示a>b;傳回值小於0，表示a&lt; b;傳回值等於0，表示a=b


那另一個參數 **Comparator**是什麼呢?

Comparator是指實作了java.util.Comparator介面的物件，是一個可加可不加的parameter，若沒有加，則值視為null，代表依原來預設的規則進行排序( compareTo() )，若有加，則改依Comparator的規則進行排序，以下是範例:
```Java
	import java.util.*;

	class ReverseStringComparator implements Comparator< ReverseStringComparator> {
		@Override
		public int compare(String s1, String s2) {
			return -s1.compareTo(s2);
		}
	}
```
需要注意的是必須要實作compare()方法，這個方法將會取代原本的比較方式，傳回值一樣是大於0、小於0或等於0，意義也都一樣

在以下的情況中，你必須使用到Comparator

1.  不能修改原始碼(修改了就不是標準API了)

2.  class被宣告為final，無法先繼承再做修改

3.  想有不只一種排序的方法


其實，String, Integer都能直接用sort()方法進行排序，最根本的原因就是他們已經實作了Comparable介面

### Further More

1.  底層的排序實現是Merge Sort，所需時間複雜度是O( nlog(n) )，且是一個stable的排序

2.  Collections裡有一個方法 **reverseOrder()** 會傳回一個Comparator，倒轉原來的排序方式

3.  若被比較的物件沒有實作Comparable介面，則會拋出 **ClassCastException** 例外。原因是當a物件與b物件進行比較時，a物件會先cast成Comparable型態，若沒實作Comparable，就會出錯